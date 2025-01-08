# clag

Distinctive tags for cloud resources.

Pseudo-random generator, pre-populated with samples for a substring with one million variants, themed for concepts like cloud, bucket and blob. Outputs as JSON for easier use with infrastructure-as-code tools including Terraform (see [Example](#example) below).

## Why?

To avoid the need for less human-readable subtrings like UUIDs.

## How?

Most simply, just run the command:

```shell
clag
```

Optionally, pass in any substrings to begin the sequence, in the order they should appear:

```shell
clag cloud 1
```

This outputs a string prefixed with 'cloud-1-', e.g. 'cloud-1-spectral-bit-spittoon'.

For more than one output string at a time, apply the `--number` / `-n` option:

```shell
clag -n 10 cloud 1
```

For JSON, use `--json` / `-j`:

```shell
clag -j cloud 1
```

This outputs a map with a single value keyed by default under 'string' and multiple as an array under 'strings'. The default key names are set close to the top of the source file.

### Example

#### Terraform

Single string output can be generated and applied via Terraform data blocks using the `--json` / `-j` option. For a generic cloud resource:

```hcl
...

data "external" "cool-bin-tag" {
  program = ["clag", "-j", "cloud", "1"]
}

resource "<resource_type>" "cool-bin" {
  <resource_argument_key> = data.external.cool-bin-tag.result.string
}

...
```

## Source

The script can be run with the command `./clag` while in the same directory, and from elsewhere using the pattern `path/to/clag`, by first making it executable, if not already, with `chmod +x clag`. Once executable, it can be run from any directory with the simpler `clag` by placing it in a directory listed on the `$PATH` environment variable, e.g. '/bin' or '/usr/bin'.

The hashbang at the top of the file assumes the presence of Bash in '/bin', the source code that several utils are installed and can be invoked directly. A list can be found close to the top of the file, and is printed also in the help text. In the event of any absence, the script will print the fact then exit.

### Defaults

The following core default values are defined close to the top of the source file:

- `ones` - modifiers used for the first element in the pseudorandomly generated substring
- `twos` - those used for the second element
- `objs` - i.e. objects, used for the third

The values are space-separated lists enclosed in parentheses, easily modified to customize the possible set of substrings generated. The current counts are listed as part of the help text (see [Options](#options) below) along with the product.

### Making changes

Running the self-test after making changes plus extending or adding test cases to cover new behaviour is recommended. The self-test is run with the `--test` or `-T` flag (see [Options](#options) below). The test cases are set in the `perform_self_test` function, which is defined with the other option handlers.

## Options

The following can be passed to `clag` before the optional substring arguments:

- `--number` / `-n`, to output a number of strings equal to the value of the next argument, e.g. 10 strings with `-n 10`
- `--json` / `-j`, to output in JSON format
- `--version` / `-v`, to show name and version number then exit
- `--help` / `-h`, to show help text, incl. substring element counts and product, then exit
- `--test` / `-T`, to perform the self-test then exit, returning an exit code of 1 on failure
