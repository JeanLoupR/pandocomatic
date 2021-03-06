## Pandocomatic configuration {#pandocomatic-configuration}

Pandocomatic can be configured by means of a *pandocomatic configuration
file*, which is a YAML file. For example, the following YAML code is a valid
*pandocomatic configuration file*:

```{.yaml}
::paru::insert ../example/manual/example_configuration_file.yaml
```

By default, pandocomatic looks for the configuration file in the *pandocomatic
data directory*; by convention this file is named `pandocomatic.yaml`.

You can tell pandocomatic to use a different configuration file via the
command-line option `--config`. For example, if you want to use a
configuration file `my-config.yaml`, invoke pandocomatic as follows:

```{.bash}
pandocomatic --config my-config.yaml some-file-to-convert.md
```

A *pandocomatic configuration file* contains two properties:

1. global `settings`
2. external `templates`

These two properties are discussed after presenting an example of a
configuration file. For more in-depth information about
pandocomatic templates, please see the [Chapter on pandocomatic
templates](#pandocomatic-templates). 

### Settings

You can configure five optional global settings:

1. `data-dir`
2. `match-files`
3. `skip`
4. `recursive`
5. `follow-links`

The latter three are used only when converting a whole directory tree with
pandocomatic. These are discussed in the next sub section.

The first setting, `data-dir` (String), tells pandocomatic where its
*data directory* is. You can also specify the *pandocomatic data directory*
via the command-line option `--data-dir`. For example, if you want to use
`~/my-data-dir` as the *pandocomatic data directory*, invoke pandocomatic as
follows:

```{.bash}
pandocomatic --data-dir ~/my-data-dir some-file-to-convert.md
```

If no *pandocomatic data directory* is specified whatsoever, pandocomatic
defaults to pandoc's data directory.

Any directory can be used as a *pandocomatic data directory*, there are no
conventions or requirements for this directory other than being a directory.
However, it is recommended to create a meaningful sub directory structure. For
example, a sub directory for processors, filters, CSL files, and pandoc
templates makes it easier to manage and point to these assets.

The setting `match-files` controls how pandocomatic selects the template to
use to convert a file. Possible values for `match-files` are `first` and
`all`. Pandocomatic matches a file to a template as follows:

1.  If the file has one or more `use-template` statements in the
    *pandocomatic* metadata, it will use these specified templates.
2.  However, if no such templates are specified in the file, pandocomatic
    tries to find *global* templates as follows:

    a.  If the setting `match-files` has value `all`, all templates with a
        glob pattern that matches the input filename are used to convert that
        input file. For example, you can specify a template `www` to convert
        `*.md` files to HTML and a template `pdf` to convert `*.md` to PDF. In
        this case, a markdown file will be converted to both HTML and PDF. For
        example, you could use this to generate a website with a print PDF page
        for each web page.  
        
    b.  If the setting `match-files` has value `first`,
        the first template with a glob pattern that matches the input file is used
        to convert the file.

        This is the default.

#### Configuring converting a directory tree {#global-settings}

You can convert a directory tree by invoking pandocomatic with a single
directory as the input rather than one or more files. Of course, once you
start converting directories, more fine-grained control over what files to
convert than "convert all files" is useful. There are four settings you can
use to control which files to convert. Three of them are global settings, the
other one is the `glob` property of an *external pandocomatic template*. The
`glob` property is discussed later.

The three global settings to control which files to convert are:

1. `recursive` (Boolean), which tells pandocomatic to convert sub directories
   or not.  This setting defaults to `true`.
2. `follow-links` (Boolean), which tells pandocomatic to treat symbolic links
   as files and directories to convert or not. This setting defaults to
   `false`.
3. `skip` (Array of glob patterns), which tells pandocomatic which files not
   to convert at all. This setting defaults to `['.*', 'pandocomatic.yaml']`:
   ignore all hidden files (starting with a period) and also ignore default
   *pandocomatic configuration files*.

#### Default configuration

Pandocomatic's default configuration file is defined in the file
[`lib/pandocomatic/default_configuration.yaml`](https://github.com/htdebeer/pandocomatic/blob/master/lib/pandocomatic/default_configuration.yaml).
This default configuration is used when

- no configuration is specified via the command-line option `--config`, and
- no default configuration file (`pandocomatic.yaml`) can be found in the
  *pandocomatic data directory*.

When converting a directory tree, each time pandocomatic enters a (sub)
directory, it also looks for a default configuration file to *update* the
current settings. In other words, you can have pandocomatic behave differently
in a sub directory than the current directory by putting a `pandocomatic.yaml`
file in that sub directory that changes the global settings or *external
pandocomatic templates*.

### Templates

Besides the global `settings` property, a *pandocomatic configuration file* can
also contain a `templates` property. In the `templates` property you define the
*external pandocomatic templates* you want to use when converting files with
pandocomatic. Pandocomatic templates are discussed in detail in the [Chapter
on pandocomatic templates](#pandocomatic-templates). The `glob` property of
*external pandocomatic templates* is related to configuring pandocomatic when
converting a directory. It
tells pandocomatic which files in a directory are to be converted with a template.

If you look at the example *pandocomatic configuration file* at the start of
this chapter, you see that the `webpage` template is configured with property `glob:
['*.md']`. This tells pandocomatic to apply the template `webpage` to all
markdown files with extension `.md`. In other words, given a directory with
the following files:

```
directory/
+ sub directory/
| + index.md
- index.md
- image.png 
```

Running pandocomatic with the example *pandocomatic configuration file* will
result in the following result"

```
directory/
+ sub directory/
| + index.html
- index.html
- image.png 
```

That is, all `.md` files are converted to HTML and all other files are copied,
recursively.
