# couchapp-compress #

`couchapp-compress` is a small Ruby script that wraps the [couchapp](https://github.com/couchapp/couchapp) command line tool and compresses JavaScript files.

## Preconditions ##

By default, `couchapp-compress` uses [UglifyJS](https://github.com/mishoo/UglifyJS), which is expected to be installed and available on the command line as `uglifyjs`, but it can be used with any other JavaScript compressor that outputs compressed files to the standard output device as well. In order to use your JavaScript compressor of choice, simply change the `COMPRESSOR` constant's value in the top of the `couchapp-compress` script.

`couchapp-compress` requires that it is invoked from your CouchApp's root directory, and that the to be compressed scripts are included via a partial in your mustache templates.

## Usage ##

In order to install it, just copy the `couchapp-compress` script into your CouchApp's root directory. Alternatively, put it to a location that's referenced in your shell's `PATH` environment variable.

In order to use it, go to your CouchApp's root directory. Invoke `couchapp-compress` with the path of the partial file that contains the scripts to be compressed as the first argument, and optionally with a destination as the second argument. When no destination is specified, the default destination from the `.couchapprc`, if present, will be used.

**Synopsis:**

```Shell
couchapp-compress <scripts_partial_path> [<destination>]
```

**Examples:**

```Shell
couchapp-compress templates/partials/scripts.html
```

```Shell
couchapp-compress templates/partials/scripts.html https://admin:secret@mambofulani.ic.ht/blog
```

```Shell
couchapp-compress templates/partials/scripts.html production
```

## How It Works ##

For instance, you've got a mustache template `templates/index.html` that (amongst others) includes a partial `templates/partials/scripts.html` via `{{>scripts}}`.

`templates/index.html`:

```HTML
<html>
  <head>{{>head}}</head>
  <body>
    {{>header}}
    <div class="content">{{>content}}</div>
  </body>
  {{>scripts}}
</html>
```

`templates/partials/scripts.html`:

```HTML
<script src="../../vendor/jquery/jquery-1.7.1.js"></script>
<script src="../../vendor/couchapp/jquery.couch.js"></script>
<script src="../../vendor/couchapp/jquery.couch.app.js"></script>
<script src="../../vendor/couchapp/jquery.couch.app.util.js"></script>
<script src="../../vendor/couchapp/jquery.evently.js"></script>
<script src="../../vendor/couchapp/jquery.mustache.js"></script>
<script src="../../script/index.js"></script>
```

What `couchapp-compress` now does is the following:

* parse `templates/partials/scripts.html`

* find the scripts that correspond to the referenced paths

* compress the scripts

* put the compressed scripts altogether into one new temporary file

* replace the content of `templates/partials/scripts.html` with a reference to the temporary file;  
  for instance, the file's content could look like `<script src="../../1323024736.min.js"></script>`

* push the CouchApp to the specified destination or rather to the default destination if none is specified  
  (i.e., execute `couchapp push [<destination>]`)

* restore the old (original) content of `templates/partials/scripts.html`

* delete the temporary file containing the compressed scripts

Note that although it creates a new file and also modifies an existing one, there won't be any changes in the CouchApp directory after executing `couchapp-compress`, as the applied changes are reverted as soon as the `couchapp push [<destination>]` command has been executed.
