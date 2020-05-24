# Doxygen

## Basic usage

* generate a config file

  ```text
  $ doxygen -g Doxygen
  ```

* change the Dosygen to generate all\(include functions\)

  ```text
  EXTRACT_ALL            = NO (to YES)
  ```

* generate document

  ```text
  $ doxygen Doxygen
  $ cd latex
  $ make
  $ gvfs-open refmax.pdf
  $ cd ../html
  $ firefox index.html
  ```

## Basic style

### for class

```text
/**
  class description
*/
```

### for function

```text
/**
  function description
  @param param1 Param1 description
  @param param2 Param2 description
  @return Return description
*/
```

### for variable

```text
int a /**< variable description */
```

