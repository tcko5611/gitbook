# cxx



## wild char transform

```text
const size_t cSize = strlen(prog);
std::wstring wc(cSize, L'#');
mbstowcs(&wc[0], prog, cSize);
Py_SetProgramName(&wc[0]);
```

## run function before main

```text
void premain() __attribute__ ((constructor));

void premain()
{
...
}
```

