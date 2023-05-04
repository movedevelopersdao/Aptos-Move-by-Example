# Move.toml

A `Move.toml` file is a manifest file that contains metadata such as name, version, and dependencies for the package.

The manifest itself contains a number of sections, primary of which are:

* `[package]`- includes package metadata such as name and version
* `[dependencies]` - specifies dependencies of the project
* `[addresses]` - address aliases

The minimal package source directory structure looks as follows:

```
package_name 
     |-Move.toml 
     |-sources 
          |- example.move
```

**Move.toml**

```toml
[package]
name = "aptos_by_examples"
version = "0.0.0"

[dependencies]
AptosFramework = { git = "https://github.com/aptos-labs/aptos-core.git", subdir = "aptos-move/framework/aptos-framework", rev = "mainnet" }

[addresses]
my_addrx = "0x42"
std = "0x1"
```
