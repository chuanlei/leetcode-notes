protobuf插件

```xml
<plugin>
  <groupId>org.xolstice.maven.plugins</groupId>
  <artifactId>protobuf-maven-plugin</artifactId>
  <configuration>
    <protocArtifact>
      com.google.protobuf:protoc:${shaded.protobuf.version}:exe:${os.detected.classifier}
    </protocArtifact>
    <!-- Place these in a location that compiler-plugin is already looking -->
    <outputDirectory>${project.build.directory}/generated-sources</outputDirectory>
    <!-- With multiple executions, this must be `false` otherwise we wipe out the previous execution -->
    <clearOutputDirectory>false</clearOutputDirectory>
  </configuration>
  <executions>
    <execution>
      <id>compile-protobuf</id>
      <phase>generate-sources</phase>
      <goals>
        <goal>compile</goal>
      </goals>
    </execution>
    <execution>
      <id>compile-grpc</id>
      <phase>generate-sources</phase>
      <goals>
        <goal>compile-custom</goal>
      </goals>
      <configuration>
        <pluginId>grpc-java</pluginId>
        <pluginArtifact>
          io.grpc:protoc-gen-grpc-java:${shaded.grpc.version}:exe:${os.detected.classifier}
        </pluginArtifact>
      </configuration>
    </execution>
  </executions>
</plugin>
```

## 生命周期

![生命周期](https://github.com/chuanlei/tech-notes/blob/master/pics/maven.jpg)


## Reactor Build Order

![reactor-build-order](https://github.com/chuanlei/tech-notes/blob/master/pics/reactor-build-order.jpg)
