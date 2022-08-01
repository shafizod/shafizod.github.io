[![providers](https://img.shields.io/badge/doc-Providers-success)](https://shafizod.github.io/providers)
[![agents](https://img.shields.io/badge/doc-Agents-success)](https://shafizod.github.io/agents)
[![invoices](https://img.shields.io/badge/doc-Invoices-success)](https://shafizod.github.io/invoices)
[![webcheckout](https://img.shields.io/badge/doc-Webcheckout-success)](https://shafizod.github.io/webcheckout)

First example:

=== "Tab 1"
    Markdown **content**.

    Multiple paragraphs.

=== "Tab 2"
    More Markdown **content**.

    - list item a
    - list item b

Second example:

=== "C"

    ``` c
    #include <stdio.h>

    int main(void) {
      printf("Hello world!\n");
      return 0;
    }
    ```

=== "C++"

    ``` c++
    #include <iostream>

    int main(void) {
      std::cout << "Hello world!" << std::endl;
      return 0;
    }
    ```

Third example:

=== "Unordered list"

    * Sed sagittis eleifend rutrum
    * Donec vitae suscipit est
    * Nulla tempor lobortis orci

=== "Ordered list"

    1. Sed sagittis eleifend rutrum
    2. Donec vitae suscipit est
    3. Nulla tempor lobortis orci

Fourth example:

!!! example

    === "Unordered List"

        ``` markdown
        * Sed sagittis eleifend rutrum
        * Donec vitae suscipit est
        * Nulla tempor lobortis orci
        ```

    === "Ordered List"

        ``` markdown
        1. Sed sagittis eleifend rutrum
        2. Donec vitae suscipit est
        3. Nulla tempor lobortis orci
        ```

Fifth example:

<hr />
=== "tab_1"

    Content of tab_1  

=== "tab_2"

    Content of tab_2  

Sixth example:

---
title: Use tabs to organize content
output: html_document
---

You can turn parallel sections to tabs in `html_document` output.

## Results {.tabset}

### Plots

We show a scatter plot in this section.

```{r, fig.dim=c(5, 3)}
par(mar = c(4, 4, .5, .1))
plot(mpg ~ hp, data = mtcars, pch = 19)
```

### Tables

We show the data in this tab.

```{r}
head(mtcars)
```