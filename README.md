﻿Terraform's gitignore will skip the following files:
- files in local directories that are locates under '/.terraform/' directory,
- files that start from anything and end on the extension '.tfstate' precisely and files that have '.tfstate.' as some part of the extension,
- all files that are named crash.log,
- files that are ending on '.tfvars',
- all files with the names override.tf and override.tf.json,
- files endinig on '_override.tf' and '_override.tf.json',
- all .terraformrc and terraform.rc files.
