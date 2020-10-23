FROM adoptopenjdk:14.0.2_8-jdk-hotspot as jdk

# java.desktop for swing: https://github.com/JetBrains/kotlin/blob/master/compiler/cli/src/org/jetbrains/kotlin/cli/common/CLITool.kt#L203
# jdk.unsupported for Sun.misc.unsafe
RUN jlink --compress=2 --module-path /opt/java/openjdk/jmods \
    --add-modules java.base,jdk.compiler,java.desktop,jdk.unsupported \
    --output /jlinked

FROM alpine:3.12 as builder

RUN apk add --no-cache curl unzip bash

RUN curl -L https://github.com/JetBrains/kotlin/releases/download/v1.4.10/kotlin-compiler-1.4.10.zip -o kotlinc.zip
RUN unzip kotlinc.zip

FROM scratch

COPY --from=0 /jlinked                    /opt/jdk/
COPY --from=0 /lib64/ld-linux-x86-64.so.2 /lib64/
COPY --from=0 /lib/x86_64-linux-gnu       /lib/x86_64-linux-gnu/
COPY --from=0 /usr/lib/locale             /usr/lib/locale/

COPY --from=1 /bin/bash                   /bin/
COPY --from=1 /lib/ld-musl-x86_64.so.1    /lib
COPY --from=1 /usr/lib/libncursesw.so.6   /usr/lib/
COPY --from=1 /usr/lib/libreadline.so.8   /usr/lib/
COPY --from=1 /kotlinc                    /opt/kotlin/
COPY kotlin                               /usr/bin/

ENTRYPOINT ["/opt/jdk/bin/java", "-Dkotlin.home=/opt/kotlin/", "-cp", "opt/kotlin/lib/kotlin-runner.jar", "org.jetbrains.kotlin.runner.Main", "-version"]
