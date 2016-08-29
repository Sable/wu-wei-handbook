# Create a new compiler

A (Wu-Wei) compiler automatically translates an implementation from its current form into a different version (in the same language or not) during the build phase of the benchmarking cycle to create a build. The transformation must only rely on information available in the source code of the implementation. It should not use any information that might be obtained by executing the implementation. Artifacts that might be good candidates to be wrapped as compilers are those that:

- perform a static translation from a higher-level language (ex: C) to a lower-level language (ex: x86 assembly). They are traditionally called compilers;
- perform a static translation from a high-level language (ex: CoffeeScript, ES6) to another high-level language (ex: JavaScript). They are sometimes called transpilers;
- perform a source-to-source transformation for a later dynamic analysis. They are traditionally called dynamic instrumenters.

Please refer to the [list of available compilers](list-available-artifacts.md#compilers) as examples before creating a new one. In the case none of those compilers suit your needs, we suggest you:

1. fork one of the existing compilers;
2. create a backward-compatible modification;
3. do a a pull request on the repository of the existing compiler. 

This way the modification will benefit current users of that artifact and it will avoid fragmentation of the compatible artifacts. If no available compiler may suit your needs please create a new one from scratch and do a pull request to add it in the [list](list-available-artifacts.md#compilers).

## Execution context

During the build phase, the working directory of the compiler is a new directory specific to the configuration that is used to create a build. The compiler script or executable may therefore create any number of temporary or throw-away files as it needs in its current working directory. 

Some artifacts may need to have access to the source code in their working directory. In this case, a script should first copy the source code into the working directory before manipulating the files. This was necessary to run the [AspectMatlab compiler](https://github.com/Sable/AspectMatlab) using a [build script](https://github.com/Sable/aspect-matlab-compiler/blob/master/instrument-in-build-directory).

## Universal description conventions

A compiler artifact should have a description file ('compiler.json') in its root directory in the JSON format. This description should contain at least the following properties:

| Property Name         | Expected Values  | Description                                                            |
| :-------------------- | :--------------- | :--------------------------------------------------------------------- |
| "type"                | "compiler"       | Specifies that the description schema is that of a compiler.           |
| "short-name"          | string           | Unique name that identifies the compiler for the commandline interface.|
| "supported-languages" | array of strings | List the names of the languages that the compiler supports as input. Should be  the [canonical name of each of the languages](https://github.com/Sable/wu-wei-handbook/blob/master/README.md#canonical-names-for-languages). |
|
| "target-languages"    | array of strings | List the names of the languages that the compiler supports as output. Should be the [canonical name of each of the languages](https://github.com/Sable/wu-wei-handbook/blob/master/README.md#canonical-names-for-languages). |
| "runner-name"         | string           | Name of the executable (or executable bundle) created by the compiler. Used by an environment for executing the build. |
| "commands"            | list of commands | List of commands to execute to perform the compilation. Each command these properties. An "executable-path" that specifies the path to the executable or an "executable-name" that specifies the name of the executable in the default execution environment (PATH on unix). A list of "options" to pass as arguments to the compiler executable. These options should refer to properties of the implementation in the configuration to retrieve the paths to the source code and compilation options. For complex workflows it is best to create a script in the compiler directory and call this script with the implementation source code as options. Refer to the [description reference](https://github.com/Sable/wu-wei-handbook/blob/master/README.md#artifact-description-json-formats) for a complete explanation of all options possible. |

The following description shows an example taken from the [Ostrich concatenate compiler for MATLAB](https://github.com/Sable/ostrich-matlab-concatenate-compiler) that simply calls a custom script that concatenate all the source files into a single file and ensure each source file has a newline at its end:  

    {
        "type": "compiler",
        "short-name":"matlab-concat",
        "supported-languages":[
            "matlab"
        ],
        "target-languages":[
            "matlab"
        ],
        "runner-name":"runner.m",
        "commands": [
            {   "executable-path": { "file": "./cat-with-nl"},
                "options": [
                    { "config": "/implementation/runner-source-file"},
                    { "config": "/implementation/core-source-files", "optional": true },
                    { "config": "/implementation/libraries", "optional": true },
                    ">",
                    { "config": "/compiler/runner-name" }
                ]
            }
        ]
    }
    
Moreover the compiler should use at least the files described in the "runner-source-file", the "core-source-files", and the "libraries" of an implementation. The last two should be optional because a benchmark may be completely contained in a single file (although it would be preferable it were not). It is free to require files or parameters in other properties of the implementation but doing so may prevent its applicability to many existing implementations. Some languages may require more properties than only those presented so far, we give more details in the next section. 

## Language-specific conventions

Some languages require more properties than the strict minimum. There specific requirements are listed here.

### C

The C compilers usually require the directories into which they need to look for the includes in the header of source files. A C implementation therefore explicitly lists those directories with the "include-directories" property which the compiler may use as an option.

Moreover some C implementations for benchmarks specify the input-size for their executable as a compile-time macro rather than a run-time parameter to the resulting executable. In those cases, the input-size macro name is defined as a compilation-flags on the implementation, which the compiler should also include in its options.

But cases are supported with the Ostrich [gcc wrapper](https://github.com/Sable/ostrich-gcc-compiler):

    {
        "type": "compiler",
        "short-name":"gcc",
        ...
        "commands": [
            {   "executable-name": "gcc",
                "options":[
    	            ...
                    {
                        "prefix":"-I",
                        "value": { "config": "/implementation/include-directories", "optional": true }
                    },
                    ...
                    { "config": "/implementation/compilation-flags", "optional": true },
                    ...
                ]
            }
        ]
    }

