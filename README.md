Creating and releasing a Play 1 module
======================================

The sections below describe the steps required to create and release a Play 1 module along with a corresponding sample application:

* The steps are based on how <a href="http://sant0s.github.io/play1-chart/" target="_blank">Play 1 Chart</a> was created
* Some steps may be redundant or missing
* The order of the steps is not strict i.e. some steps may be executed earlier/later than specified
* Some steps may vary according to user preferences (e.g. preferred text editor)
* `<>` refers to a placeholder (e.g. `<module>` is a placeholder for a module name)
* Linux, Eclipse and Play 1.3 are assumed to be part of the development environment
* UNIX commands are assumed to take place on the path determined by the most recently executed `cd` command

Create module
-------------

1. Create module: `play new-module <module>`
  - The module name must match regex `[a-zA-Z]+`
1. Build module: `cd <module>; play build-module`
  - Press enter when asked about required Play Framework versions
1. Add JAR files required by the module to the `lib` directory
1. Eclipsify module: `cd ..; play eclipsify <module>`
1. Import module project into Eclipse:
  - `File -> Import -> General -> Existing Project into Workspace`
1. Add module code to the `src` directory under package `play.modules.<module>`
1. Add module fast tags to under `app/tags`
1. Write and execute tests
1. Add documentation to `documentation/manual/home.textile`
1. Document all public API methods using Javadoc
1. Execute `play javadoc` and check that:
  1. All public API methods are documented
  1. No module related Javadoc errors are found in log files (under the `logs` directory)
1. Check that no code problems are reported by Eclipse
1. Check that no unnecessary logging is generated by the module (search the code for `Logger`)
1. Build module: `cd <module>; play build-module`
  - Press enter when asked about required Play Framework versions

Create module sample application
--------------------------------

1. Create sample application under the module's directory: `cd <module>; play new samples-and-tests; cd samples-and-tests`
  - Enter `Play <module> Module App` when asked about the application name
1. Add `samples-and-tests/app` as a source folder in Eclipse (in order to navigate through source files)
1. Set `<module>` as a sample application's dependency during development: see [Appendix A](#appendixa)
1. Resolve dependencies: `play deps`
1. File `<module>` shows up under the modules directory and its content is the path to the module
1. Check that the module can be used:
  1. `play <module>:hello`: `Hello` is displayed
  1. `play run`: `INFO ~ Module <module> is available (<path to module>)` is displayed
1. Implement the sample application
1. Write and execute tests

Release module
--------------

1. `cd <module>`
1. Remove unnecessary files/directories (e.g. `conf/messages`, `conf/routes`)
1. `rm -fr dist; play build-module`
  - Press enter when asked about required Play Framework versions
1. Test the sample application
  1. `cd samples-and-tests`
  1. `play test` and execute automated tests (`http://localhost:9000/@tests`)
  1. `play clean; play deps; play run` and test manually (`http://localhost:9000`)
1. Configure the sample application for release
  1. Add `<module>` as a sample application's dependency for release: see [Appendix B](#appendixb)
  1. `play deps; play clean`
  1. Remove unnecessary directories: `javadoc`, `modules` and `test-result`
  1. Check that no unnecessary files exist
1. Configure the module for release and archive it
  1. Set `<module>` dependencies for release: see [Appendix C](#appendixc)
  1. `cd <module>; rm -fr dist; play build-module`
    - Press enter when asked about required Play Framework versions
  1. A zip file is generated under `<module>/dist`
  1. Exclude unnecessary files from the module's zip file: `zip -d <module>-<version>.zip README commands.pyc`
  1. Exclude any other unnecessary files/directories:
    1. Files: `.classpath`, `.project`, `.gitignore`
    1. Directories: `.settings`, `eclipse`, `javadoc`, `logs`, `.git`, empty directories (e.g. `public`)
  1. Archive the module's zip file under a release directory (e.g. outside the module directory, in a directory named `<release date>`)
  1. `rm -fr dist`
1. Add module to local Git repository: see [Appendix D](#appendixd)
1. Publish module on GitHub: see [Appendix E](#appendixe)
1. Create module web page in GitHub: see [Appendix F](#appendixf)
1. Post a module registration request on the play-framework Google Group using the e-mail template specified in [Appendix G](#appendixg)

<a name="appendixa"></a>Appendix A - Sample application dependencies during development
---------------------------------------------------------------------------------------

In order to set `<module>` as a sample application's dependency during development, set the contents of the sample application's `conf/dependencies.yml` to:

```
# Application dependencies

require:
    - play
    - <module> -> <module> <version>

repositories:
    - <repository>:
        type: local
        artifact: "<path to module>"
        contains:
            - <module> -> *
```

where <version> is the module version (e.g. `0.1`).

**NB: `dependencies.yml` must not include any tab characters.**

<a name="appendixb"></a>Appendix B - Sample application dependencies for release
--------------------------------------------------------------------------------

In order to set `<module>` as a sample application's dependency for release, set the contents of the sample application's `conf/dependencies.yml` to:

```
# Application dependencies

require:
    - play -> <module> <version>
```

where <version> is the module version (e.g. `0.1`).

**NB: `dependencies.yml` must not include any tab characters.**

<a name="appendixc"></a>Appendix C - Module dependencies for release
--------------------------------------------------------------------

In order to configure the module's dependencies for release, set the contents of the module's `conf/dependencies.yml` to:

```
self: play -> <module> <version>

require:
    - <module>
```

where <version> is the module version (e.g. `0.1`).

**NB: `dependencies.yml` must not include any tab characters.**

<a name="appendixd"></a>Appendix D - Add module to local Git repository
-----------------------------------------------------------------------

1. `cd <module_directory>`
1. `git init`1. `vi .gitignore` and include (at least):

        .classpath
        eclipse
        .project
        .settings
        commands.pyc
1. `vi .gitignore` and include (at least):

        .classpath
        eclipse
        .project
        .settings
        commands.pyc

1. `vi README` (recommended for all GitHub repositories):

        # play1-<module>
        Play 1 <module> module

        Home page: http://<user>.github.io/play1-<module>/

1. `vi LICENSE` (recommended for all GitHub repositories)
1. `git status`: all files should be unstaged
1. Add all files recursively: `git add .`
1. `git status`: all files should be staged
1. Commit: `git commit -m "Play 1 <module> module <version>"`
1. Tag project: `git tag <tagname>` (e.g. `git tag 0.1.0`)
1. `git status`: all files should be committed i.e. on the master HEAD of the local copy

<a name="appendixe"></a>Appendix E - Publish module on GitHub
-------------------------------------------------------------

1. Create repository in GitHub:
  1. Log in GitHub
  1. New repository
  1. Name: `play1-<module>`
  1. Description (optional): Play 1 `<module>` module
  1. Public
  1. Create repository
  1. Note the repository SSH URL: `git@github.com:<user>/play1-<module>.git`
1. Push local repository to GitHub one:
  1. `cd <module>`
  1. Associate GitHub (remote) repository with local one: `git remote add origin <repository SSH URL>`
  1. Push files from local repository to GitHub: `git push -u origin master`
  1. Push tag to remote: `git push origin <tagname>` (e.g. `git push origin 0.1.0`)

<a name="appendixf"></a>Appendix F - Create module web page in GitHub
---------------------------------------------------------------------

1. `cd <module release directory>` (where the module's zip file resides)
1. Convert module's textile documentation to GitHub's markdown: `pandoc <path to module>/documentation/manual/home.textile -t markdown -o home.md`
1. In Github, go to the repository, press Settings and then automatic page generator
1. Copy/paste the contents of `home.md` onto the body
1. Press "Continue to layouts"
1. Select layout
1. Preview page and check that everything's ok, as GitHub Markdown is different from Markdown (adjust text if necessary)
1. Publish page

<a name="appendixg"></a>Appendix G - Module registration request e-mail template
--------------------------------------------------------------------------------

```
Subject: [play-framework] [1.3.x] Module registration request: <module>
To: play-framework@googlegroups.com


Module name:
<module>
 
Display name:
<module display name>
 
Description:
The <module display name> module <module's purpose>.
 
Project home page:
http://<user>.github.io/<module repository name on GitHub>/
```

References
----------

* <a href="https://www.playframework.com/documentation/1.3.x/modules" target="_blank">Play modules</a>
* <a href="https://www.packtpub.com/books/content/play-framework-introduction-writing-modules" target="_blank">Play Framework: Introduction to Writing Modules | PACKT Books</a>