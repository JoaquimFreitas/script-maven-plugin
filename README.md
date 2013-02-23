[![Build Status](https://api.travis-ci.org/alexec/script-maven-plugin.png)](https://travis-ci.org/alexec/script-maven-plugin)

Overview
===
This plugin allows you to execute scripts as part of your Maven build. It uses Apache Bean Scripting Framework under the hood, so you can use any languages supported by that framework. Support for some languages is in-built. Custom languages (e.g. JRuby) can be added with a couple of XML.

See [this list](http://svn.apache.org/repos/asf/commons/proper/bsf/trunk/src/main/java/org/apache/bsf/Languages.properties) for languages, and [this list](http://en.wikipedia.org/wiki/List_of_Java_scripting_languages) of scripting languages on the JVM.

By default, the following are bound as a beans:

    project, artifactMetadataSource, artifactResolver, artifactFactory, localRepository

Examples
===
Example 1 - BeanShell
---
    <execution>
        <phase>package</phase>
        <goals><goal>execute</goal></goals>
        <configuration>
            <!-- beanshell is the default language -->
            <script>
                System.out.println(project.getName());
            </script>
        </configuration>
    </execution>
    ...
    <dependencies>
        <dependency>
            <groupId>org.beanshell</groupId>
            <artifactId>bsh</artifactId>
            <version>2.0b5</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>

Example 2 - Groovy
---
    <executions>
        <execution>
            <phase>package</phase>
            <goals><goal>execute</goal></goals>
            <configuration>
                <!-- language auto-detected -->
                <scriptFile>script.groovy</scriptFile>
            </configuration>
        </execution>
    </executions>
    <dependencies>
    <dependencies>
        <dependency>
            <groupId>org.codehaus.groovy</groupId>
            <artifactId>groovy</artifactId>
            <version>1.8.3</version>
        </dependency>
    </dependencies>

Example 3 - JRuby
---
    <executions>
        <execution>
            <phase>package</phase>
            <goals><goal>execute</goal></goals>
            <configuration>
                <language>ruby</language>
                <scriptFile>script.rb</scriptFile>
            </configuration>
        </execution>
    </executions>
    <dependencies>
        <dependency>
            <groupId>org.jruby</groupId>
            <artifactId>jruby</artifactId>
            <version>1.7.2</version>
        </dependency>
    </dependencies>

Example 4 - JavaScript
---
This example also includes an additional dependency.

    <executions>
        <execution>
            <phase>package</phase>
            <goals><goal>execute</goal></goals>
            <configuration>
                <language>javascript</language>
                <script>
                    importPackage(java.lang);
                    importPackage(org.apache.commons.io);
                    importPackage(java.io);

                    System.out.println(project.getName());
                    FileUtils.touch(new File("marker"));
                </script>
            </configuration>
        </execution>
    </executions>
    <dependencies>
        <dependency>
            <groupId>org.mozilla</groupId>
            <artifactId>rhino</artifactId>
            <version>1.7R3</version>
        </dependency>
        <!-- additional import for doing file I/O -->
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.4</version>
        </dependency>
    </dependencies>

Example 5 - Listing Beans
---
You might wish to list the beans available:

    mvn script:list-beans

Example 6 - Complex Javascript
---
You can do some funky stuff with the beans:

    importPackage(java.lang);
    importPackage(java.io);

    System.out.println(project.getName());

    var a = artifactFactory.createArtifact("commons-io", "commons-io", "2.4", "compile", "jar");
    artifactResolver.resolve(a, project.getRemoteArtifactRepositories(), localRepository);

    System.out.println("locally at " + a.getFile());
    System.out.println("versions " + artifactMetadataSource.retrieveAvailableVersions(a, localRepository, project.getRemoteArtifactRepositories()));

Known Issues
===
- It'd be better to use JSR-223, but I need to find a reliable framework first!

Appendices
===
Appendix A - Languages Table
---
    Language     Engine                                                  File Extensions  Activity/Popularity
    ---          ---                                                     ---              ---
    javascript   org.apache.bsf.engines.javascript.JavaScriptEngine      js               High/High
    jacl         org.apache.bsf.engines.jacl.JaclEngine                  jacl,tcl         High/Low
    netrexx      org.apache.bsf.engines.netrexx.NetRexxEngine            nrx              Med/Low
    groovy       org.codehaus.groovy.bsf.GroovyEngine                    groovy           High/High
    scala        com.alexecollins.maven.plugin.ScalaEnginge              scala            High/High
    java         org.apache.bsf.engines.java.JavaEngine                  java             High/High
    javaclass    org.apache.bsf.engines.javaclass.JavaClassEngine        class            High/High
    bml          org.apache.bml.ext.BMLEngine                            bml              Low/Low
    vbscript     org.apache.bsf.engines.activescript.ActiveScriptEngine  vbs              High/Low
    jscript      org.apache.bsf.engines.activescript.ActiveScriptEngine  jss              "
    perlscript   org.apache.bsf.engines.activescript.ActiveScriptEngine  pls              "
    perl         org.apache.bsf.engines.perl.PerlEngine                  pl               High/Low
    jpython      org.apache.bsf.engines.jpython.JPythonEngine            py               High/Med
    jython       org.apache.bsf.engines.jython.JythonEngine              py               High/Med
    lotusscript  org.apache.bsf.engines.lotusscript.LsEngine             lss              Low/Low
    xslt         org.apache.bsf.engines.xslt.XSLTEngine                  xslt             High/High
    pnuts        pnuts.ext.PnutsBSFEngine                                pnut             Low/Low
    beanbasic    org.apache.bsf.engines.beanbasic.BeanBasicEngine        bb               Low/Low
    beanshell    bsh.util.BeanShellBSFEngine                             bsh              High/Med
    ruby         org.jruby.javasupport.bsf.JRubyEngine                   rb               High/Med
    judoscript   com.judoscript.BSFJudoEngine                            judo,jud         Low/Low

&#x2020; Activity of the Language
* High - release in last 12 months AND active website
* Med - no stable release XOR no active website
* Low - no recent releases AND no active website

&#x2021; Popularity on the Java Platform
* Guess work!
