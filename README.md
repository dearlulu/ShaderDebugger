# ShaderDebugger

ShaderDebugger is a library built for [SHADERed](https://github.com/dfranx/SHADERed).
It can be used to run shaders on CPU or debug them (get variable values, run them line by line, etc...).

## How?
ShaderDebugger uses [glsl-parser](https://github.com/dfranx/glsl-parser) to parse 
GLSL into AST. The AST is converted to the bytecode with the [aGen](https://github.com/dfranx/aGen) library. The bytecode can then be executed using the [BlueVM](https://github.com/dfranx/BlueVM).

## How to compile
Run these commands to set up and compile the project:
```bash
git clone https://github.com/dfranx/ShaderDebugger.git
cd ShaderDebugger
git submodule init
git submodule update
cmake .
make
```

Run the program:
```bash
./bin/ShaderDebugger
```

## Example
```c++
std::string src = ... your shader code ...;
bv_library* libGLSL = sd::Library::GLSL();

sd::ShaderDebugger dbg;
dbg.SetSource<sd::GLSLTranslator>(sd::ShaderType::Pixel, src, "main", libGLSL);

dbg.SetValue("iFactor", 0.7f);
dbg.SetValue("iColor", "vec3", glm::vec3(0.5f, 0.6f, 0.7f));

bv_variable ret = dbg.Execute();

glm::vec3 outColor = sd::AsVec3(*dbg.GetValue("outColor"));
printf("outColor = vec3(%.2f, %.2f, %.2f);\n", outColor.x, outColor.y, outColor.z);

bv_variable_deinitialize(&ret);
bv_library_delete(libGLSL);

return 0;
```

Or execute line by line:
```c++
while (vs.Step()) {
    // get various info using these functions: GetCurrentFunction, GetFunctionStack, GetVariableValue, etc...
}
```

## Limitations
List of things that currently don't work but I plan to fix them:
### GLSL
- doesn't support macros, interface blocks, local & structure member arrays & global variable initalization (unless it has const keyword)
- parsed, but not stored & not implemented: auxilary, precision, memory, invariant
- parsed & stored but not implemented: smooth, flat, noperspective

### HLSL
- not implemented yet

### Both
- function overloading (BlueVM)