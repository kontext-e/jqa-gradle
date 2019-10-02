This is a blueprint for a Gradle project using jQAssistant.

TL;DR
-----
Add this to your build.gradle and adapt the plugins to your needs:

    project.ext["jqaversion"] = "1.7.0"
    project.ext["kejqapluginversion"] = "1.7.0"
    
    configurations {
        jqa
    }
    
    dependencies {
        jqa("com.buschmais.jqassistant.cli:jqassistant-commandline-neo4jv3:${project.jqaversion}") {
            exclude module: 'asm'
        }
    
        // list here all the plugins you want to use
        jqa("com.buschmais.jqassistant.plugin:java:${project.jqaversion}")
        jqa("com.buschmais.jqassistant.plugin:junit:${project.jqaversion}")
    
        // and plugins from other contributors
        jqa("de.kontext-e.jqassistant.plugin:jqassistant.plugin.git:${project.kejqapluginversion}")
    }
    
    task jqaclean(type: Delete) {
        delete 'jqassistant/report'
        delete 'jqassistant/store'
    }
    
    task(jqascan, dependsOn: 'jqaclean', type: JavaExec) {
        main = 'com.buschmais.jqassistant.commandline.Main'
        classpath = configurations.jqa
    
        // this is needed to work around a windows limitation
        // of a too long command path
        // see https://stackoverflow.com/questions/50707110/gradle-windows-java-io-ioexception-createprocess-error-206-filename-is-too-lon
        def cp = CollectionUtils.join(File.pathSeparator, classpath.getFiles())
        environment 'CLASSPATH', cp
        classpath = classpath.filter { false }
    
        args 'scan'
        args '-f'
    
        args 'java:classpath::build/classes/main'
        args 'java:classpath::build/classes/test'
    
        rootProject.subprojects {
            args 'java:classpath::'+it.name+'/build/classes/main'
            args 'java:classpath::'+it.name+'/build/classes/test'
        }
    
        args '.git'
    }
    
    task(jqaanalyze, type: JavaExec) {
        main = 'com.buschmais.jqassistant.commandline.Main'
        classpath = configurations.jqa
    
        // this is needed to work around a windows limitation
        // of a too long command path
        // see https://stackoverflow.com/questions/50707110/gradle-windows-java-io-ioexception-createprocess-error-206-filename-is-too-lon
        def cp = CollectionUtils.join(File.pathSeparator, classpath.getFiles())
        environment 'CLASSPATH', cp
        classpath = classpath.filter { false }
    
        args 'analyze'
    }
    
    task(jqaavailablescopes, type: JavaExec) {
        main = 'com.buschmais.jqassistant.commandline.Main'
        classpath = configurations.jqa
    
        // this is needed to work around a windows limitation
        // of a too long command path
        // see https://stackoverflow.com/questions/50707110/gradle-windows-java-io-ioexception-createprocess-error-206-filename-is-too-lon
        def cp = CollectionUtils.join(File.pathSeparator, classpath.getFiles())
        environment 'CLASSPATH', cp
        classpath = classpath.filter { false }
    
        args 'available-scopes'
    }
    
    task(jqareset, type: JavaExec) {
        main = 'com.buschmais.jqassistant.commandline.Main'
        classpath = configurations.jqa
    
        // this is needed to work around a windows limitation
        // of a too long command path
        // see https://stackoverflow.com/questions/50707110/gradle-windows-java-io-ioexception-createprocess-error-206-filename-is-too-lon
        def cp = CollectionUtils.join(File.pathSeparator, classpath.getFiles())
        environment 'CLASSPATH', cp
        classpath = classpath.filter { false }
    
        args 'reset'
    }
    
    task(jqaeffectiverules, type: JavaExec) {
        main = 'com.buschmais.jqassistant.commandline.Main'
        classpath = configurations.jqa
    
        // this is needed to work around a windows limitation
        // of a too long command path
        // see https://stackoverflow.com/questions/50707110/gradle-windows-java-io-ioexception-createprocess-error-206-filename-is-too-lon
        def cp = CollectionUtils.join(File.pathSeparator, classpath.getFiles())
        environment 'CLASSPATH', cp
        classpath = classpath.filter { false }
    
        args 'effective-rules'
    }
    
    task(jqaavailablerules, type: JavaExec) {
        main = 'com.buschmais.jqassistant.commandline.Main'
        classpath = configurations.jqa
    
        // this is needed to work around a windows limitation
        // of a too long command path
        // see https://stackoverflow.com/questions/50707110/gradle-windows-java-io-ioexception-createprocess-error-206-filename-is-too-lon
        def cp = CollectionUtils.join(File.pathSeparator, classpath.getFiles())
        environment 'CLASSPATH', cp
        classpath = classpath.filter { false }
    
        args 'available-rules'
    }
    
    task(jqareport, type: JavaExec) {
        main = 'com.buschmais.jqassistant.commandline.Main'
        classpath = configurations.jqa
    
        // this is needed to work around a windows limitation
        // of a too long command path
        // see https://stackoverflow.com/questions/50707110/gradle-windows-java-io-ioexception-createprocess-error-206-filename-is-too-lon
        def cp = CollectionUtils.join(File.pathSeparator, classpath.getFiles())
        environment 'CLASSPATH', cp
        classpath = classpath.filter { false }
    
        args 'report'
    }
    
    task(jqa, dependsOn: ['jqascan','jqaanalyze']) {
        jqaanalyze.mustRunAfter jqascan
    }
    
    task(jqs, type: JavaExec) {
        main = 'com.buschmais.jqassistant.commandline.Main'
        classpath = configurations.jqa
    
        // this is needed to work around a windows limitation
        // of a too long command path
        // see https://stackoverflow.com/questions/50707110/gradle-windows-java-io-ioexception-createprocess-error-206-filename-is-too-lon
        def cp = CollectionUtils.join(File.pathSeparator, classpath.getFiles())
        environment 'CLASSPATH', cp
        classpath = classpath.filter { false }
    
        args 'server'
    
        standardInput = System.in
    }
    
    clean.dependsOn jqaclean
