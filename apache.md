# Archivo de consulta para los comandos de Apache Maven
## Creando un proyecto Maven desde cero primario
Para poder crear un nuevo proyecto debemos de ir a la carpeta donde queremos crear un proyecto y escribir el siguiente comando:

mvn archetype:generate
-DgroupId=com.udemy.maven
-DartifactId=mi-primer-proyecto-maven
-DarchettypeArtifactId=maven-archetype-quickstart
-DarchetypeVersion=1.4
-DinteractiveMode=false


mvn archetype:generate -DgroupId=com.udemy.maven -DartifactId=mi-primer-proyecto-maven -DarchettypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false

## ¿Como construir el proyecto?
- Validate: Verifica que el archivo pom.xml este en orden y que toda la información necesaria para ejecutar el proyecto este disponible
- Compile: Compila las funciones y el código fuente
- Test: Compila las fuentes de prueba y luego ejecuta las pruebas utilizando un marco de prueba adecuado
- Package: Empaqueta los artefactos en el formato especificado en el archivo pom.xml, empaqueta el códigocompilado en su formato de distribución.
- Verify: Esta fase ejecuta controles para verificar que el paquete es válido
- Install: Esta fase instala el paquete en el repositorio local.
- deploy: Esta fase instala el paquete final en el repositorio configurado

Para construir el proyecto nos famos a la carpeta contenedora y damos package
mvn package

## Repositorio de Maven
Maven realiza una revisión de donde queremos traer los archivos necesarios para el proyecto, por lo tanto hay tres tipos de repositorios
- Local: En nuestro equipo
- Remoto: Es el repositorio donde se descargan los archivos Maven requeridos
- Espejos (Nexus, Artifact): Repositorios administrados

Estos archivos se descargan en C:/Users/username/.m2/repository

### Cambiar la ubicación de los repositorios de Maven
Ubicación de como revisa maven:
primer revisa en 
username/.m2/settings.xml
maven_home/conf/settings.xml 
username/.m2/repository
### Para ejecutar Maven en un servidor Proxy
- id: Es el identificador único para el proxy
- active Debe ser true si el proxy esta activo. Es útil para declarar un conjunto de proxies.
- protocol, host, port Es el empleado para describir el protocolo, el host y el puerto del proxy. La forma de emplearlo es protocol://host:port
- nonProxyHosts: Es una lista de hosts que no debe de ser proxy. el delimitador de la lista el tipo esperado del servidor proxy.

## Perfiles en Maven
Los perfiles son importantes para la portabilidad de un proyecto y estan destinados a una indicación en concreto como desarrollo y producción,
Los perfiles pueden separarse por;
### Tipo de perfiles
- Por proyecto: Definido en el propio POM
    - Por Usuario: Definido en la configuración de Maven en el archivo username/.m2/settings.xml
-Global: Definido en la configuración global de Maven en el archivo Maven_home/conf/settings.xml

Para saber que perfiles estan activos ponemos en consola dentro de la carpeta:
mvn help:active-profiles

Para correr un package con el perfil dev ponemos
mvn package -P dev

Para saber más información sobre en donde se esta ejecutando Maven para sacar por ejemplo el sistema operativo ponemos:
mvn enforcer:display-info

### Tipo de Propiedades
- Variables de entorno Sirven para prefijar una variable con env ${env.PATH}
- Variables POM Sirven para prefijar una variable con project ${project.version}
- Variables de configuración Sirven para prefijar una variable con settings ${env.PATH}
- Propiedades normales Ej
<properties>
    <java.version>1.8</java.version>
</properties>
${java.version} devolvera 1.8

Para conocer el arbol de dependencias ponemos mvn dependency:tree

Para meter un plugin que se ejecute clean antes de que se compile ponemos dentro del pom dentro de build
<plugins>
    <plugin>
        <artifactId>maven-clean-plugin</artifactId>
        <version>3.1.0</version>
        <executions>
            <execution>
                <id>auto-clean</id>
                <phase>initialize</phase>
            </execution>
            <goals>
                <goal>clean</goal>
            </goals>
        </executions>
    </plugin>
</plugins>

Para ejecutar las pruebas damos 
mvn test

Para ejecutar las pruebas sin Surefire ponemos
mvn -DskipTests test

Para deshabilitar las pruebas desde el pom ponemos
<plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-surefire-plugin</artifactId>
        <version>2.22.1</version>
        <configuration>
            <skipTests>true</skipTests>
        </configuration>
    </plugin>
</plugins>

### Maven Resource
Para que los recursos del programa se copien en la carpeta target
mvn process:resources
mvn resources:resources
mvn resources:testResources
Invocando cualquier fase que siga a la frase process:resources

Para poder decirle a Maven que necesitamos en el target recursos ponemos:
<resources>
    <resource>
        <directory>src/main/resources</directory>
    </resource>
    <resource>
        <directory>src/main/configuraciones</directory>
    </resource>
</resources>

### SonarCube
Es una plataforma para gestionar la calidad del código, esta herramienta es un poco complicada de trabajar por todos los pasos que son necesarios para poder ponerla,
pero si es importante ponerla 

## Scope de dependencias
Estos Scope estan puestos para poner las dependencias según su scope
- compile Esta dependencia es necesaria para la compilación
- test Esta depenencia solo es necesaria para las pruebas
- runtime Estas dependencias no son necesarias durante la compilación
- provide Esta le dice a Maven que la dependencia es necesaria para la compilación y el runtime 
- system Similar a provide, aqui debemos proporcionar explicitamente la ubicación del archivo jar
- imort solo se usa en una dependencia del tipo pom en la sección dependencyManagement

<groupId>junit</groupId>
<artifactId>juinit</artifactId>
<version>11</version>
<scope>test</scope>

## Maven Dependency 
Para obtener la lista de dependencias
mvn dependency:list 
mvn dependency:tree 

Para que las dependencias se copien dentro de target ponemos
mvn dependency:copy-dependencies

Para que las dependencias se cipien en un fichero que queramos ponemos
mvn dependency:copy-dependencies -DoutputDirectory=target/dependencias

Para copiar las dependencias a como estan puestas en el pom tambien al target ponemos 
mvn dependency:copy-dependencies -Dmdep.copyPom=true -Dmdep.useRepositoryLayout=true

Para detectar dependencias no utilizadas o no declaradas ponemos
mvn dependency:analyze

## Maven Site
Para poder generar la documentación de un proyecto ponemos 
mvn site

Para generar documentación con Javadocs ponemos en el pom ponemos:
<reporting>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>org.apache.maven.plugins</artifactId>
            <version>3.3.2</version>

            <!-- Para quitar los ejemplos ponemos -->
            <reportSets>
                <reportSet>
                    <reports>
                        <report>javadoc</report>
                    </reports>
                </reportSet>
            </reportSets>
        </plugin>

        <!-- Si deseamos un reporte de las pruebas unitarias del proyecto y agregarlo al sitio web de documentación -->

        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-report-plugin</artifactId>
            <version>3.0.0-MS</version>
        </plugin>
    </plugins>
</reporting>
y luego usar mvn site


## Maven Offline
Para que podamos trabajar fuera de linea ponemos:
mvn dependency:go-offline
mvn -o clean package

## Maven Loggs
Niveles de logging de Maven
- Debug: Mensajes detallados
- Info: Mensajes de información, Nivel de mensajes por defecto.
- Error: Mensajes de error

Para que maven se este quieto y solo muestre mensajes de nivel de error correriamos 
mvn -q clean package

Por defecto nos muestra mensajes de nivel info y error
mvn clean package

Para mostrar todos los mensajes e nivel debbug y superior
mvn -X clean package

Para que el log se almacene en un archivo se pone:
mvn -X clean apckage > log.txt

Otra opción:
mvn -X clean package -l log.txt

## Maven Help
- effective-pom Muestra el fichero pom que será usado por Maven después de aplicar la herencia, la interpolación y los perfiles
- all-profiles Muestra todos los perfiles que están disponibles para el proyecto
- active-profiles Muestra la lista de perfiles activos
- describe Es empleado para obtener el detalle de cualquier plugin

Effective pom es para saber que pom se ha utiliado al final para el proyecto
mvn help:effective-pom > effective-pom.xml

mvn help:all-profiles

mvn help:active-profiles

mvn help:describe -DgroupId=org.apache.maven.plugins -DartifactId=maven-clean-plugin -Ddetail=true

# Para generar un archivo war en vez de jar al crear el proyecto ponemos
mvn archetype:generate
-DgroupId=com.udemy.maven
-DartchetypeGroupId=org.apache.maven.archetypes
-DartifactId=arquetipo-maven-webappp
-DarchettypeArtifactId=maven-archetype-webapp
-DarchetypeVersion=1.4
-DinteractiveMode=false

mvn archetype:generate -DgroupId=com.udemy.maven -DartifactId=arquetipo-maven-webappp -DarchettypeArtifactId=maven-archetype-webapp -DarchetypeVersion=1.4 -DinteractiveMode=false

# Para generar un archivo ear en vez de jar al crear el proyecto ponemos
mvn archetype:generate
-DgroupId=com.udemy.maven
-DartifactId=arquetipo-maven-ear
-DartchetypeGroupId=org.wildfly.archetype
-DarchettypeArtifactId=wildfly-javaee7-webapp-ear-archetype
-DarchetypeVersion=8.2.0.Final
-DinteractiveMode=false

mvn archetype:generate -DgroupId=com.udemy.maven -DartifactId=arquetipo-maven-ear -DartchetypeGroupId=org.wildfly.archetype -DarchettypeArtifactId=wildfly-javaee7-webapp-ear-archetype -DarchetypeVersion=8.2.0.Final -DinteractiveMode=false

# Desplegar en Jetty

mvn org.eclipse.jetty:jetty-maven-plugin:run

run es lo mismo que package y despues despliega

# Uber jar
Es un archivo jar que incorpora las dependencias necesarias
Para crearlo podemos hacer lo siguiente:
- Unshaded: descomprime todos los archivos jar y luego vuelve a emparejarlos en un único jar
- jar de jars: El archivo jar final contiene los otros archivos jar integrados
- shaded: Igual que Unshaded renombra todos los paquetes de todas las dependencias

# Maven Exec Plugin
Plugin que permite ejecutar cualquier sistema ejecutable

mvn clean package
mvn exec:java -Dexec.mainClass="com.udemy.maven.App"

# Actualizaciones de los plugins y dependencias de Maven
mvn versions:display-plugin-updates
mvn versions:display-dependency-updates