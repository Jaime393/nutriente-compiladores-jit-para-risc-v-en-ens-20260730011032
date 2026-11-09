# 🧪 Nutriente Exótico: Compiladores JIT para RISC-V en ENS

**Región de origen:** Senegal  
**Cazado el:** 20260730011032  

---

## 📦 Tesoros encontrados en GitHub:
- No se encontraron repositorios relacionados.

---

## 🌍 ¿Por qué es valioso?
Este tema es escaso porque proviene de ecosistemas de desarrollo no occidentales. Contiene aproximaciones únicas a la IA que no están en los papers mainstream de EEUU/Europa.

---

# Compiladores JIT para RISC-V

> Lo que sigue es la pieza sembrada. El bloque de arriba es el README inicial del
> repo, conservado tal cual. Su afirmación sobre el valor del tema es palabra del
> README inicial, no una verificación mía.

## Qué es un JIT

Un compilador JIT (*just-in-time*) traduce código a código máquina nativo mientras
el programa corre, en lugar de hacerlo todo antes. Observa qué métodos se ejecutan
de verdad, los compila con esa información (perfilado, inlining especulativo) y
recompila cuando sus suposiciones dejan de cumplirse. Es el motor de Java (HotSpot),
JavaScript (V8), WebAssembly (Wasmtime) y de casi todo lenguaje gestionado moderno.

Portar un JIT a una arquitectura nueva no es recompilar: el generador de código,
el parcheo en caliente y la gestión de la caché de instrucciones hay que
reescribirlos para esa ISA. RISC-V tiene cuatro rasgos que cambian ese trabajo.

## Qué tiene de específico RISC-V para un JIT

**1. Sin códigos de condición.** RISC-V no tiene *flags* como x86: una comparación
escribe su resultado en un registro y el salto lo examina después. Un JIT pensado
para x86 tiene que expandir cada condición en una secuencia de instrucciones, y el
código generado crece. Es uno de los choques documentados al portar Cogit, el JIT
de Pharo, a RISC-V (MPLR '22).

**2. Longitud variable de instrucción.** Con la extensión C, las instrucciones de
16 y 32 bits se mezclan libremente (alineación a 16 bits). La C reduce el tamaño
del código entre un 25 % y un 30 %, y entre el 50 % y el 60 % de las instrucciones
de un programa pueden reemplazarse por variantes comprimidas. Pero un JIT que
parchea código en caliente —sustituir un salto por otro, por ejemplo— ya no puede
suponer que cada instrucción ocupa lo mismo: el parcheo tiene que contar parcelas
de 16 bits.

**3. La caché de instrucciones no se entera sola.** Cuando un JIT escribe código
nuevo en memoria, el procesador puede seguir ejecutando lo viejo que quedó en la
caché de instrucciones. RISC-V exige sincronizar explícitamente con `FENCE.I`
(extensión Zifencei), el único mecanismo estándar para ello. El propio manual de
la ISA menciona a los compiladores JIT: pueden generar una traza larga de
instrucciones antes de un solo `FENCE.I` y amortizar así el coste. En Linux, la
ABI retiró `FENCE.I` del nivel de usuario y exige una llamada al sistema para
mantener la coherencia.

**4. No hay "un RISC-V".** La ISA es modular: base (RV32I/RV64I) más extensiones
(M, A, F, D, C, V, B...). Un JIT no puede suponer un conjunto fijo de
instrucciones; tiene que detectar en tiempo de ejecución qué extensiones existen
en ese chip y compilar para ellas. El puerto de OpenJDK muestra hasta qué punto:
su línea de compilación nombra una lista de extensiones larga
(`rv64i2p1_m2p0_a2p1_f2p2_d2p2_c2p0_v1p0_zi…`) y distingue perfiles como RVA23.

## Implementaciones reales y su estado honesto

| Runtime | Estado verificado |
|---|---|
| **V8** (JavaScript / WebAssembly) | Puerto abierto en julio de 2020; más de 15.000 casos de prueba pasando en HiFive Unleashed con Fedora. Fusionado en el upstream en febrero de 2021; el soporte RV32GC se fusionó el 29 de julio de 2022. En diciembre de 2025 estaba mayormente a la par de x86_64 y ARM64, corre la suite completa de JetStream y es uno de los puertos más verdes en los buildbots de V8. |
| **OpenJDK HotSpot** (Java) | Puerto totalmente funcional, con los dos compiladores C1 y C2, en JDK 21 y JDK 17; JDK 11 en curso (FOSDEM 2024). |
| **LLVM ORC / JITLink** | El backend RISC-V de JITLink llegó en julio de 2021. El enlazador antiguo (RuntimeDyld) no soporta RISC-V; solo JITLink lo hace. Por eso Mesa tuvo que migrar de MCJIT a ORC JIT para llevar llvmpipe a RISC-V. |
| **Cranelift / Wasmtime** (WebAssembly) | Cranelift soporta riscv64. Wasmtime en riscv32 no tiene JIT nativo: corre con el intérprete Pulley (estado a enero de 2025). |
| **Pharo Cogit** | Puerto en curso; se apoya en una suite de unas 1.400 pruebas agnóstica a la ISA que ya sirvió para portar la VM a ARMv8 sin hardware (MPLR '22). |
| **TornadoVM + OCK** (Java con vectores) | Cadena JIT en dos etapas sobre RISC-V con la extensión vectorial: en un Spacemit K1 de 8 núcleos, multiplicación de matrices hasta 33x más rápida que Java secuencial y hasta 4,6x que Java multinúcleo (RISC-V Summit Europe 2025). |
| **LuaJIT** | Intento temprano (FOSDEM 2020): Lua 5.1 corriendo en Spike y en un soft-core en FPGA; registros, inmediatos y etiquetas en curso y todo lo demás pendiente. Sin puerto completo verificado. |

## Rendimiento: lo que hay medido

Lo medido con números y fuente es poco, y es de casos concretos, no del JIT
en general:

- **TornadoVM/OCK en Spacemit K1** (8 núcleos, OpenJDK 21.0.5, TornadoVM 1.0.10):
  de 4,1x a 33x sobre Java secuencial y de 2x a 4,6x sobre Java multinúcleo en
  multiplicación de matrices, según el tamaño. En matrices pequeñas (hasta
  64×64) Java secuencial gana: el JIT no compensa.
- **V8 en HiFive Unleashed** (2020): más de 15.000 casos de prueba y los
  benchmarks Sunspider y Kraken corriendo. No hay en esa fuente una cifra de
  rendimiento contra x86.
- **C extension**: 25–30 % menos tamaño de código; 50–60 % de las instrucciones
  reemplazables por variantes comprimidas. Es tamaño, no velocidad.

No hay una comparativa pública verificada de "el mismo JIT en RISC-V contra el
mismo JIT en x86 en el mismo hardware" — y no la hay porque el mismo hardware
no existe.

## Límites

- **La paridad no es igualdad.** Que V8 en RISC-V esté "a la par de
  características" no significa que rinda igual que en x86_64: significa que
  pasa las mismas pruebas y corre las mismas suites.
- **El puerto de 32 bits se apaga.** V8 deprecó RV32: se mantendría hasta mayo
  de 2026 y se eliminaría si nadie lo defendía con un caso de uso fuerte. RISC-V
  de 32 bits vive sobre todo en sistemas pequeños sin sistema operativo, y V8
  necesita un sistema operativo.
- **Los JIT pequeños van detrás.** LuaJIT no tiene puerto completo; DartVM y
  OpenJ9 estaban en desarrollo o planificados en 2022, sin dato posterior
  verificado aquí.
- **El hardware manda.** Gran parte del trabajo de portar un JIT a RISC-V se
  hizo en QEMU o en simulador por falta de hardware fiable; el propio equipo de
  Cogit lo señala como límite.

## NO SÉ

- Qué significa "ENS" en el nombre de este repo. No hay fuente que lo aclare.
- Si el puerto RV32 de V8 fue eliminado después de mayo de 2026.
- El estado actual del puerto de LuaJIT a RISC-V más allá del dato de 2020.
- Si existe una comparativa JIT contra AOT en el mismo chip RISC-V con la
  misma carga.
- El estado actual de DartVM y OpenJ9 en RISC-V (último dato verificado: 2022).
- Qué significa "Cazado en Senegal" ni si la afirmación del README inicial
  sobre ecosistemas no occidentales se sostiene: es palabra del README inicial,
  sin interpretar.
