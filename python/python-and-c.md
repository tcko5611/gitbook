# Python and C



## init module

```text
static PyMethodDef DspfPyMethods[] = {
  {"rptDspfRes",  dspfpy_rpt, METH_VARARGS,
   "Execute rpt dspf res."},
  {NULL, NULL, 0, NULL}        /* Sentinel */
};
```

| Field | C Type | Meaning |
| :--- | :--- | :--- |
| ml\_name | const char \* | name of the method |
| ml\_meth | PyCFunction | pointer to the C implementation |
| ml\_flags | int    flag bits | indicating how the call should be constructed |
| ml\_doc | const char | points to the contents of the docstring |

## module init function

```text
PyMODINIT_FUNC
initdspfpy(void)
{
    PyObject *m;

    m = Py_InitModule("dspfpy", DspfPyMethods); // can have multiple function in it
    if (m == NULL)
        return;
    // define exception for the module
    DspfPyError = PyErr_NewException("dspfpy.error", NULL, NULL);
    Py_INCREF(DspfPyError);
    PyModule_AddObject(m, "error", DspfPyError);
}
```

## c function

```text
static PyObject *
dspfpy_rpt(PyObject *self, PyObject *args)
{
  char *dspfNm, * subNm, *outNm;
  double rmin, rmax;
  char *scope, *rmscope;
  int num, num_max_reff,sort;
  char *skip;
  int mt;
  // parse needed arguments for c function
  if (!PyArg_ParseTuple(args, "sssddssiiisi", &dspfNm, &subNm, &outNm, &rmin, 
            &rmax, &scope, &rmscope, &num, &num_max_reff, &sort,
            &skip,&mt))
    return NULL;
  // call the actual c function
  int sts = rptDspfResWrapper(dspfNm, subNm, outNm, rmin, rmax, scope, rmscope, 
              num, num_max_reff, sort, skip, mt);
  if (sts < 0) {
    PyErr_SetString(DspfPyError, "rptdspfres command failed");
    return NULL;
  }
  // return function
  return PyLong_FromLong(sts);
}
```

## used by python

Build into lib

```text
import dspfpy
dspfpy.rptDspfRes(...)
```

## python3

### define exception and throw exception

define the exception object pointer

```text
static PyObject *dspfpyError;
```

you need to create the exception object in init module\(see below for init module\)

### define c function and python function mapping

```text
static PyMethodDef dspfpy_methods[] = {
  { "rptDspfRes",  (PyCFunction)dspfpy_rpt, METH_VARARGS,
    "Execute rpt dspf res."},
  { "dspfRead",  (PyCFunction)dspfpy_dspfRead, METH_VARARGS,
    "Read dspf file."},
  {NULL, NULL, 0, NULL}
};
```

colums: 1. rptDspfRes: the python function call name 2. dspfpy\_rpt: c function

### define moduledef

```text
static struct PyModuleDef dspfmodule = {
  PyModuleDef_HEAD_INIT,
  "dspfpy", /* name of module */
  NULL,
  -1, /* size of per-interpreter state of the module, -1 for global varaibles */
  dspfpy_methods /* method mapping*/
};
```

### init module to use

```text
PyMODINIT_FUNC
PyInit_dspfpy(void)
{
  PyObject *m = PyModule_Create(&dspfmodule); /* create module */
  if (m == NULL)
    return NULL;
  /*create exception to use*/
  dspfpyError = PyErr_NewException("dspfpy.error", NULL, NULL); 
  Py_INCREF(dspfpyError);
  PyModule_AddObject(m, "error", dspfpyError);
  return m;
}
```

### python c function writing

```text
/* destructor of c point */
static void dspfpy_deleteNtl(PyObject *p) {
  if (PyCapsule_CheckExact(p)) {
    printf("correct ntl pyobject\n");
  }
  else {
    printf("not correct ntl pyobject\n");
  }
}
/* python c function implementation */ 
static PyObject *
dspfpy_dspfRead(PyObject *self, PyObject *args)
{
  char *dspfNm;
  if (!PyArg_ParseTuple(args, "s", &dspfNm))
    return NULL;
  void *ntl = dspfRead(dspfNm);
  if (ntl == NULL) {
    PyErr_SetString(dspfpyError, "dspfRead Failed");
    return NULL;
  }
  /* translate c pointer to python object*/
  return PyCapsule_New(ntl, NULL, dspfpy_deleteNtl);
}

/* python c function implementation */ 
static PyObject *
dspfpy_rpt(PyObject *self, PyObject *args)
{
  PyObject *dspf;
  char *dspfNm, *subNm, *outNm;
  double rmin, rmax;
  char *scope, *rmscope;
  int num, num_max_reff,sort;
  char *skip;
  int mt;
  /* O: PyObject *, s : string, i: integer */
  if (!PyArg_ParseTuple(args, "Osssddssiiisi", &dspf, &dspfNm, &subNm, &outNm, &rmin, 
            &rmax, &scope, &rmscope, &num, &num_max_reff, &sort,
            &skip,&mt))
    return NULL;
  void *ntl = PyCapsule_GetPointer(dspf, NULL);
  int sts = rptDspfResWrapper(ntl, dspfNm, subNm, outNm, rmin, rmax, scope, rmscope, 
                  num, num_max_reff, sort, skip, mt);
  if (sts < 0) {
    PyErr_SetString(dspfpyError, "rptDspfRes Failed");
    return NULL;
  }
  return PyLong_FromLong(sts);
}
```

## python 2

### define exception and throw exception

define the exception object pointer

```text
static PyObject *dspfpyError;
```

you need to create the exception object in init module\(see below for init module\)

### define c function and python function mapping

```text
static PyMethodDef dspfpy_methods[] = {
  { "rptDspfRes",  (PyCFunction)dspfpy_rpt, METH_VARARGS,
    "Execute rpt dspf res."},
  {NULL, NULL, 0, NULL}
};
```

colums: 1. rptDspfRes: the python function call name 2. dspfpy\_rpt: c function

### init module to use

```text
PyMODINIT_FUNC
initdspfpy(void)
{
    PyObject *m;

    m = Py_InitModule("dspfpy", DspfPyMethods);
    if (m == NULL)
        return;
    /* exception */
    DspfPyError = PyErr_NewException("dspfpy.error", NULL, NULL);
    Py_INCREF(DspfPyError);
    PyModule_AddObject(m, "error", DspfPyError);
}
```

### python c function writing

```text
/* python c function implementation */ 
static PyObject *
dspfpy_rpt(PyObject *self, PyObject *args)
{
  PyObject *dspf;
  char *dspfNm, *subNm, *outNm;
  double rmin, rmax;
  char *scope, *rmscope;
  int num, num_max_reff,sort;
  char *skip;
  int mt;
  /* O: PyObject *, s : string, i: integer */
  if (!PyArg_ParseTuple(args, "Osssddssiiisi", &dspf, &dspfNm, &subNm, &outNm, &rmin, 
            &rmax, &scope, &rmscope, &num, &num_max_reff, &sort,
            &skip,&mt))
    return NULL;
  void *ntl = PyCapsule_GetPointer(dspf, NULL);
  int sts = rptDspfResWrapper(ntl, dspfNm, subNm, outNm, rmin, rmax, scope, rmscope, 
                  num, num_max_reff, sort, skip, mt);
  /* throw exception */
  if (sts < 0) {
    PyErr_SetString(dspfpyError, "rptDspfRes Failed");
    return NULL;
  }
  return PyLong_FromLong(sts);
}
```

