# Fuentes — Compiladores JIT para RISC-V

Cada fuente con su URL, su fecha y qué afirma de lo escrito en el README.
Ningún número del README carece de fuente.

## 1. Ducasse, Polito, Tesone, Cotret, Lagadec — "Porting a JIT Compiler to RISC-V: Challenges and Opportunities", MPLR '22
- **URL:** https://hal.science/hal-03725841v1/file/default.pdf
- **Fecha:** septiembre de 2022 (MPLR '22, Bruselas).
- **Qué afirma:** los choques al portar Cogit (JIT de Pharo) a RISC-V — falta de
  códigos de condición, expansión de instrucciones por combinación simple,
  modificación de código en caliente —; la suite de unas 1.400 pruebas agnóstica
  a la ISA; que V8 ya estaba portado por el PLCT Lab, LuaJIT y DartVM en
  desarrollo, y JikesRVM y OpenJ9 planificados o sin JIT.

## 2. Robbin Ehn — "Unleashing RISC-V in Managed Runtimes", FOSDEM 2024
- **URL:** https://pretalx.fosdem.org/media/fosdem-2024/submissions/T9HHXG/resources/UnleashingRV_Final_bMFKUDX.pdf
- **Fecha:** febrero de 2024.
- **Qué afirma:** el puerto RISC-V de OpenJDK HotSpot es totalmente funcional,
  con C1 y C2, en JDK 21 y JDK 17 (JDK 11 en curso); el JIT compila métodos
  calientes con perfilado e inlining especulativo; la lista de extensiones
  `-march` que el puerto usa; el estado de Zjid.

## 3. RISC-V International — "Unlocking JavaScript: V8-RISCV Open Sourced"
- **URL:** https://riscv.org/blog/unlocking-javascript-v8-riscv-open-sourced/
- **Fecha:** 26 de agosto de 2020.
- **Qué afirma:** el backend V8 para RV64 (I, M, A, F, D y Zifencei) abierto en
  julio de 2020; más de 15.000 casos de prueba pasando y los benchmarks
  Sunspider y Kraken corriendo en HiFive Unleashed con Fedora; que con V8
  llegan JavaScript, Node.js y WebAssembly a RISC-V.

## 4. RISE Project — "A Glimpse Into V8 Development for RISC-V"
- **URL:** https://riseproject.dev/2025/12/09/a-glimpse-into-v8-development-for-risc-v/
- **Fecha:** 9 de diciembre de 2025.
- **Qué afirma:** el puerto RISC-V de V8 está mayormente a la par de x86_64 y
  ARM64, corre la suite completa de JetStream (~33 MB de bytecode Wasm y ~2M
  de líneas de JavaScript) y es uno de los puertos más verdes en los buildbots;
  el puerto de 32 bits se deprecó y se mantendría hasta mayo de 2026.

## 5. RISC-V SW Dev (Google Groups) — "Congrats! The Google V8 JavaScript Engine now supports RV32GC"
- **URL:** https://groups.google.com/a/groups.riscv.org/g/sw-dev/c/zeml52WHAU4/m/hMbGwJzHAgAJ
- **Fecha:** 29 de julio de 2022.
- **Qué afirma:** el puerto RV32GC de V8 se fusionó en el upstream de Google
  ese día; V8 soporta ya backends de 32 y 64 bits para RISC-V; el equipo del
  PLCT lo mantendría a largo plazo.

## 6. LLVM Discourse — "ORC JIT Weekly #37: JITLink RISCV"
- **URL:** https://discourse.llvm.org/t/orc-jit-weekly-37-jitlink-riscv-swift-obj-c-support-executionsession-requires-executorprocesscontrol/58670
- **Fecha:** 26 de julio de 2021.
- **Qué afirma:** el backend JITLink ELF/RISC-V llegó a LLVM esa semana
  (commit 0ad562b4), obra de Stephen Fan.

## 7. RISC-V Graphics SIG — actas de la sesión del 3 de marzo de 2023
- **URL:** https://lists.riscv.org/g/sig-graphics/messages?expanded=1&msgnum=195
- **Fecha:** 3 de marzo de 2023.
- **Qué afirma:** RuntimeDyld no soporta RISC-V y solo JITLink lo hace; el
  trabajo de llevar llvmpipe de Mesa a RISC-V es la migración de MCJIT a
  ORC JIT con JITLink; Mesa 22.3 probada en openEuler con soporte JIT.

## 8. Bytecode Alliance — Cranelift
- **URL:** https://cranelift.dev/
- **Fecha:** consultada en noviembre de 2026.
- **Qué afirma:** Cranelift es el backend de compilación de Wasmtime para JIT
  y AOT, y soporta x86-64, aarch64, s390x y riscv64.

## 9. Wasmtime — issue #7311 "embedded support" (archivo Zulip)
- **URL:** https://bytecodealliance.github.io/zulip-archive/stream/225357-git-wasmtime/topic/wasmtime.20.2F.20issue.20.237311.20embedded.20support.html
- **Fecha:** actualización del 29 de enero de 2025.
- **Qué afirma:** en riscv32 Wasmtime corre con el intérprete Pulley; no hay
  objetivo de compilación nativa de 32 bits en Cranelift ni en Winch; el
  soporte de 32 bits llegó por Pulley.

## 10. Anton Kuzmin — "Port luajit to RISC-V", FOSDEM 2020
- **URL:** https://archive.fosdem.org/2020/schedule/event/riscv_luajit/attachments/slides/4170/export/events/attachments/riscv_luajit/slides/4170/luajit_riscv_fosdem_2020_02_01.pdf
- **Fecha:** 1 de febrero de 2020.
- **Qué afirma:** LuaJIT es un JIT de traza para Lua 5.1; el puerto corría
  Lua 5.1 en Spike y en un soft-core en FPGA, con la codificación base rv32i,
  registros, inmediatos y etiquetas en curso y todo lo demás pendiente.

## 11. "Leveraging RISC-V Vectorization: Accelerating Java Programs with TornadoVM and OCK", RISC-V Summit Europe 2025
- **URL:** https://pure.manchester.ac.uk/ws/portalfiles/portal/361946410/main.pdf
- **Fecha:** mayo de 2025 (RISC-V Summit Europe, París).
- **Qué afirma:** cadena JIT en dos etapas (TornadoVM + oneAPI Construction
  Kit) sobre RISC-V con vectores; en un Spacemit K1 de 8 núcleos con OpenJDK
  21.0.5 y TornadoVM 1.0.10, la multiplicación de matrices acelera de 4,1x a
  33x sobre Java secuencial y de 2x a 4,6x sobre Java multinúcleo; en matrices
  de hasta 64×64, Java secuencial gana.

## 12. Manual de la ISA RISC-V — capítulo "Zifencei" (FENCE.I)
- **URL:** https://mirror.iscas.ac.cn/git/github.com/riscv/riscv-isa-manual/-/commit/f371bd48fa9167ebfdc8300255703c5141e57c84
- **Fecha:** el capítulo Zifencei, versión 2.0 (el commit refleja su traslado
  fuera del capítulo base).
- **Qué afirma:** `FENCE.I` es el único mecanismo estándar para que un
  almacenamiento en memoria de instrucciones sea visible a la búsqueda de
  instrucciones del mismo hart; el manual dice literalmente que los
  compiladores JIT pueden generar una traza larga antes de un solo `FENCE.I`;
  en sistemas con cachés incoherentes, `FENCE.I` puede vaciar toda la caché
  de instrucciones. La ABI de Linux retiró `FENCE.I` del nivel de usuario y
  exige una llamada al sistema.

## 13. Manual de la ISA RISC-V — extensión C (instrucciones comprimidas)
- **URL:** https://www.five-embeddev.com/riscv-user-isa-manual/latest-adoc/c-st-ext
- **Fecha:** manual vigente, consultado en noviembre de 2026.
- **Qué afirma:** la extensión C añade codificaciones de 16 bits; típicamente
  el 50–60 % de las instrucciones de un programa pueden reemplazarse por
  variantes comprimidas, con una reducción del tamaño del código del 25–30 %;
  las instrucciones de 16 y 32 bits se mezclan libremente con alineación a
  16 bits.
