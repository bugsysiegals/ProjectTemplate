---
layout: page
---
## Configuration
There are two types of configuration:

* **ProjectTemplate configuration** are the settings which alter how `load.project()` behaves when executed.  For example, whether to have logging enabled.
* **Project specific configuration** are the settings which make sense only to a particular project, but you would like to change them easily in `src` or `munge` scripts.  For example, you may define `plot_footnote = "My Proj"` to control a consistent look and feel for plots.

Both types are stored in the `config` object accessible from the global environment.  The function `project.config()` will display the current configuration, including project specific configuration.

### ProjectTemplate configuration
The current `ProjectTemplate` configuration settings exist in the `config/global.dcf` file:

* `data_loading`: This can be set to 'on' or 'off'. If `data_loading` is on, the system will load data from both the `cache` and `data` directories with `cache` taking precedence in the case of name conflict. By default, `data_loading` is on.
* `data_loading_header`: This can be set to 'on' or 'off'. If `data_loading_header` is on, the system will load text data files, such as CSV, TSV, or XLSX, treating the first row as header.
* `data_ignore`: A comma separated list of files to be ignored when importing from the `data/` directory. Regular expressions can be used but should be delimited (on both sides) by `/`. The default is to ignore no files. Note that filenames and filepaths should *never* begin with a `/`, entire directories under `data/` can be ignored by adding a trailing `/`. See [Mastering ProjectTemplate](./mastering.html) for more details.
* `cache_loading`: This can be set to 'on' or 'off'. If `cache_loading` is on, the system will load data from the `cache` directory before any attempt to load from the `data` directory. By default, `cache_loading` is on.
* `recursive_loading`: This can be set to 'on' or 'off'. If `recursive_loading` is on, the system will load data from the `data` directory and all its sub difrectories recursively. By default, `recursive_loading` is off.
* `munging`: This can be set to 'on' or 'off'. If `munging` is on, the system will execute the files in the `munge` directory sequentially using the order implied by the `sort()` function. If `munging` is off, none of the files in the `munge` directory will be executed. By default, `munging` is on.
* `logging`: This can be set to 'on' or 'off'. If `logging` is on, a logger object using the `log4r` package is automatically created when you run `load.project()`. This logger will write to the `logs` directory. By default, `logging` is off.
* `logging_level`: The value of `logging_level` is passed to  a logger object using the `log4r` package during logging when when you run `load.project()`.  By default, `logging` is INFO.
* `load_libraries`: This can be set to 'on' or 'off'. If `load_libraries` is on, the system will load all of the R packages listed in the `libraries` field described below. By default, `load_libraries` is off.
* `libraries`: This is a comma separated list of all the R packages that the user wants to automatically load when `load.project()` is called. These packages must already be installed before calling `load.project()`. By default, the reshape2, plyr, tidyverse, stringr and lubridate packages are included in this list.
* `as_factors`: This can be set to 'on' or 'off'. If `as_factors` is on, the system will convert every character vector into a factor when creating data frames; most importantly, this automatic conversion occurs when reading in data automatically. If 'off', character vectors will remain character vectors. By default, `as_factors` is on.
* `data_tables`: This can be set to 'on' or 'off'. If `data_tables` is on, the system will convert every data set loaded from the `data` directory into a `data.table`. By default, `data_tables` is off.
* `attach_internal_libraries`: This can be set to 'on' or 'off'. If `attach_internal_libraries` is on, then every time a new package is loaded into memory during `load.project()` a warning will be displayed informing that has happened. By default, `attach_internal_libraries` is off.
* `cache_loaded_data`: This can be set to 'on' or 'off'. If `cache_loaded_data` is on, then data loaded from the `data` directory during `load.project()` will be automatically cached (so it won't need to be reloaded next time `load.project()` is called).  By default, `cache_loaded_data` is on for newly created projects.  Existing projects created without this configuration setting will default to off.  Similarly, when `migrate.project()` is called in those cases, the default will be off.
* `sticky_variables`: This is a comma separated list of any project-specific  variables that should remain in the global environment after a `clear()` command.  This can be used to clear the global environment, but keep any large datasets in place so they are not unnecessarily re-generated during `load.project()`.  Note that any this will be over-ridden if the `force=TRUE` parameter is passed to `clear()`. By default, `sticky_variables` is NONE

You can override the values in `global.dcf` when loading the project by providing the option with the new setting:

    > load.project(cache_loading = FALSE) # load the project without loading from the cache
    > reload.project(cache_loading = FALSE, # Don't load from cache
                     data_ignore = '*.tsv') # Don't load tsv files

For backward compatibility it is still possible to provide a list of options, both as an unnamed
argument or named `override.config`:

    > load.project(list(cache_loading = FALSE)) # load the project without loading from the cache
    > reload.project(override.config = list(cache_loading = FALSE, # Don't load from cache
                                            data_ignore = '*.tsv')) # Don't load tsv files

Note that this behavior might be removed in a future version of `ProjectTemplate`.

### Project specific configuration
The project specific configuration is specified in the `lib/globals.R` file using the `add.config` function.  This will contain whatever is relevant for your project, and will look something like this:

    > add.config(
        keep_data = FALSE,                # should temporary data be kept?
        header = "Private & Confidential" # header in reports
      )

Note that commas need to be present after each config item except the last.  Comments can also be inserted to document what each config variable does.
To use project specific configuaration in any `lib`, `munge` or `src` script, simply use the form `config$keep_data`.
`ProjectTemplate` will automatically load project specific content in `lib/globals.R` before any other file in `lib`, so the filename should not be changed.
The `add.config()` function can also be used anywhere in the project.  So if a particular analysis in `src` wanted to override the value in `globals.R`, you can simply add the relevant `add.config()` command to the top of that script.

Another option to override the setting is to pass it to `load.project()` as with normal options:

    > load.project(keep_data = TRUE)

This only works for calls to `add.config` with the parameter `apply.override` set
to `TRUE`, for the moment this parameter defaults to `FALSE`. The `globals.R` file
in the standard `full` template therefore includes two calls to `add.config` with
an explanation which section could be overridden.
