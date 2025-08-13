# Configure this provider with a local filesystem mirror

- https://developer.hashicorp.com/terraform/cli/config/config-file#explicit-installation-method-configuration

Clone this repo

```bash
git clone git@github.com:skelly02/terraform-provider-nftower.git
cd terraform-provider-nftower
```

Create a local Terraform providers directory

```bash
# IMPORTANT: the dirname cannot include the 'v' that the git tag has
mkdir -p "$HOME/terraform-providers/registry.terraform.io/skelly02/nftower/$(git describe  --tags | sed -e 's|^v||g')/darwin_arm64"
```

Build the provider into the directory

```bash
go build -o "$HOME/terraform-providers/registry.terraform.io/skelly02/nftower/$(git describe  --tags | sed -e 's|^v||g')/darwin_arm64/terraform-provider-nftower_$(git describe  --tags)"
```

It should look like this

```bash
$ tree $HOME/terraform-providers/
/Users/<username>/terraform-providers/
└── registry.terraform.io
    └── skelly02
        └── nftower
            └── 0.11.4+pipeline-ce-workdir
                └── darwin_arm64
                    └── terraform-provider-nftower_v0.11.4+pipeline-ce-workdir

```

Make a local [cli config file](https://developer.hashicorp.com/terraform/cli/config/config-file#provider-installation) if it does not exist

```bash
touch ~/.terraformrc
```

Fill the file with contents that look like this;

```
# ~/.terraformrc
provider_installation {
  filesystem_mirror {
    path    = "/Users/<username>/terraform-providers" # point this to your directory
    include = ["registry.terraform.io/skelly02/nftower"]
  }
  direct {
    exclude = ["registry.terraform.io/skelly02/nftower"] # others (e.g., hashicorp/aws) come from the registry
  }
}
```

In your Terraform project `main.tf` file, use a block like this;

```terraform
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
    nftower = {
      source = "skelly02/nftower"
      # See installation notes https://github.com/skelly02/terraform-provider-nftower/blob/pre-run-script/NOTES.md
    }
  }
}
```

In your Terraform project, re-run `terraform init -upgrade` and then check with `terraform validate` and `terraform plan`

-----

# Configure this provider with Terraform dev overrides

- https://developer.hashicorp.com/terraform/cli/config/config-file#development-overrides-for-provider-developers

**NOTE:** You should not use this method it is very flaky and can cause issues with your other TF providers

Clone this repo

```bash
git clone git@github.com:skelly02/terraform-provider-nftower.git
cd terraform-provider-nftower
```

build the Provider

```bash
go build -o terraform-provider-nftower
```

make local [cli config file](https://developer.hashicorp.com/terraform/cli/config/config-file#provider-installation) if it does not exist

```bash
touch ~/.terraformrc
```

add these contents to the file `~/.terraformrc`

```
# ~/.terraformrc
# https://developer.hashicorp.com/terraform/cli/config/config-file#development-overrides-for-provider-developers

provider_installation {
  dev_overrides {
    "skelly02/nftower" = "/Users/<your-username-here>/projects/terraform-provider-nftower"
  }
  direct {}
}

```

update your project Terraform .tf file

```terraform
terraform {
  required_providers {
    nftower = {
      // replace this;
      // source  = "healx/nftower"
      // version = "0.11.4"

      // with this;
      source  = "skelly02/nftower"
    }
  }
}
```

re-run `terraform init -upgrade` inside your Terraform project dir

you might get an error `Failed to query available provider packages` but the subsequent `terraform apply` commands should work regardless