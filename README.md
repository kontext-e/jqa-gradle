This is a blueprint for a Gradle project using jQAssistant.

== TL;DR
Add this to your build.gradle:

        project.ext["jqaversion"] = "1.2.0"
        configurations {
            jqaRuntime
        }
        dependencies {
            jqaRuntime("com.buschmais.jqassistant:jqa-uber-parent:${project.jqaversion}")
            jqaRuntime("com.buschmais.jqassistant:jqassistant.plugin.plugin-parent:${project.jqaversion}")
            jqaRuntime("com.buschmais.jqassistant.core:plugin:${project.jqaversion}")
            jqaRuntime("com.buschmais.jqassistant.core:scanner:${project.jqaversion}")
            jqaRuntime("com.buschmais.jqassistant.core:report:${project.jqaversion}")
            jqaRuntime("com.buschmais.jqassistant.core:store:${project.jqaversion}")
            jqaRuntime("com.buschmais.jqassistant.core:shared:${project.jqaversion}")
            jqaRuntime("com.buschmais.jqassistant.plugin:common:${project.jqaversion}")
            jqaRuntime("com.buschmais.jqassistant:commandline:${project.jqaversion}")
            jqaRuntime("com.buschmais.jqassistant:neo4jserver:${project.jqaversion}")

            jqaRuntime("com.buschmais.jqassistant.plugin:java:1.2")
            jqaRuntime("com.buschmais.jqassistant.plugin:junit:1.2")
        }

        task removeJQAReport(type: Delete) {
            delete 'jqassistant/report'
            delete 'jqassistant/store'
        }

        task(jqascan, dependsOn: 'removeJQAReport', type: JavaExec) {
            main = 'com.buschmais.jqassistant.commandline.Main'
            classpath = configurations.jqaRuntime
            args 'scan'
            args '-p'
            args 'jqassistant/jqassistant.properties'
            args '-f'

            args 'java:classpath::build/classes/main'
            args 'java:classpath::build/classes/test'

            /* in a multi subprojects project it would be:
            rootProject.subprojects {
                args 'java:classpath::'+it.name+'/build/classes/main'
                args 'java:classpath::'+it.name+'/build/classes/test'
            }
            */
        }

        task(jqaanalyze, type: JavaExec) {
            main = 'com.buschmais.jqassistant.commandline.Main'
            classpath = configurations.jqaRuntime
            args 'analyze'
            args '-r'
            args 'jqassistant/jqassistant-rules'
        }

        task(jqa, dependsOn: ['jqascan','jqaanalyze']) {
            jqaanalyze.mustRunAfter jqascan
        }

        task(jqs, type: JavaExec) {
            main = 'com.buschmais.jqassistant.commandline.Main'
            classpath = configurations.jqaRuntime
            args 'server'
            standardInput = System.in
        }

        clean.dependsOn removeJQAReport
