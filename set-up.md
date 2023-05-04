---
description: Installing IDE and environment setup.
---

# Set-Up

**Install Move IDE**

To install it you'll need:

1. VSCode (version 1.43.0 and above) - you can [get it here](https://code.visualstudio.com/download); if you already have one - proceed to the next step;
2. Move-Analyzer IDE - once VSCode is installed, follow [this link](https://marketplace.visualstudio.com/items?itemName=move.move-analyzer) to install the newest version of IDE.

**Install Aptos CLI**

You can [get it here.](https://aptos.dev/tools/install-cli/)

**Running code using aptos CLI:**

Firstly you folder structure should be like this:

```sh
move_project
      |-sources
           |-first.move
      |Move.toml
```



**first.move:**

```rust
module my_addrx::Sample
{
    use std::debug;

    fun sample_function()
    {
        debug::print(&12345);
    }

    #[test]
    fun testing()
    {
        sample_function();
    }
}
```

**Move.toml:**

```toml
[package]
name = "move_project"
version = "0.0.0"

[dependencies]
AptosFramework = { git = "https://github.com/aptos-labs/aptos-core.git", subdir = "aptos-move/framework/aptos-framework", rev = "mainnet" }

[addresses]
my_addrx = "0x42"
std = "0x1"
```

**Compile the module:**

Run `aptos move compile` in vscode terminal for compiling the module.

**Test the module:**

Run `aptos move test` in vscode terminal for running the unit test.

{% hint style="info" %}
Check [this out](https://aptos.dev/cli-tools/aptos-cli-tool/use-aptos-cli/) for more information on Aptos CLI.
{% endhint %}
