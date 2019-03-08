###






## some error fix
1、Q：Room - Schema export directory is not provided to the annotation processor so we cannot export the schema
	A: just add section below in the defaultConfig .
	```groovy
	javaCompileOptions {
    annotationProcessorOptions {
        arguments = ["room.schemaLocation": "$projectDir/schemas".toString()]
    }
}
	```