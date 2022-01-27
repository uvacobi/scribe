# compgen_scribe

This repository contains the scribe for the course on Computational Genomics.

## Adding a new chapter

Create a markdown file with the contents for the chapter. We recommend using a two digit numeric followed by the name of the chapter, for example, 01-primer.md. Add the name to the config.yml as an input file. For example, see how we add the chapter "01-primer.md" to config.yml. Then type 

```{bash}
make
```

on the command line to update the scribe.pdf file. After you are satisfied, you can push the updates to the repository.

