---
tags: latex
toc: True
---

<https://www.rpi.edu/dept/arc/training/latex/LaTeX_symbols.pdf>

# pifont
![image](https://user-images.githubusercontent.com/67637935/120926335-605fd300-c717-11eb-8306-322115f14e81.png)
# Listing,Figure

```
\begin{lstlisting}[language=C, caption=An example requiring fuzzing and concolic execution to work together.]
int main(void) {
    config_t *config = read_config();
    if (config == NULL) {
        puts("configuration syntax error");
        return 1;
    }
    if (config->magic != MAGIC_NUMBER) {
        puts("Bad magic number");
        return 2;
    }
    initialize(config);
    
    char *directive = config->directives[0];
    if (!strcmp(directive, "crashstring")) {
        program_bug();
    }
    else if (!strcmp(directive, "set_option")) {
        set_option(config->directives[1]);
    }
    else {
        default();
    }
}
\end{lstlisting}


출처: https://ndb796.tistory.com/342 [안경잡이개발자]
```
