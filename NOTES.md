clone this repo

```bash
git clone git@github.com:skelly02/terraform-provider-nftower.git
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