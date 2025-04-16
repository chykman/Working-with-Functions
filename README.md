# Working-with-Functions

# AWS Environment Switching Utility

## Description
A simple Bash script to switch between AWS CLI profiles for different environments like `local`, `testing`, and `production`.

## Features
- Environment input validation
- AWS CLI installation check
- Profile existence check
- Easy switching using environment variables

---

## Script

```bash
#!/bin/bash
# AWS Environment Switcher v1.0
# Usage: ./aws_env.sh [local|testing|production]

# Check if correct number of arguments is provided
check_num_of_args() {
  if [ "$#" -ne 1 ]; then
    echo "Usage: $0 <environment>"
    echo "Environments: local, testing, production"
    exit 1
  fi
}

# Check if AWS CLI is installed
check_aws_cli() {
  if ! command -v aws &> /dev/null; then
    echo "Error: AWS CLI not found."
    exit 1
  fi
}

# Activate the chosen environment
activate_environment() {
  case "$1" in
    local)
      export AWS_PROFILE=default
      ;;
    testing|production)
      export AWS_PROFILE=$1
      ;;
    *)
      echo "Error: Invalid environment. Use local, testing, or production."
      exit 1
      ;;
  esac
  echo "Switched to '$1' environment. (AWS_PROFILE=$AWS_PROFILE)"
}

# Check if the AWS profile is configured
check_profile_exists() {
  if ! aws configure list-profiles | grep -q "^$AWS_PROFILE$"; then
    echo "Error: AWS profile '$AWS_PROFILE' not found."
    exit 1
  fi
}

# Main
main() {
  check_num_of_args "$@"
  check_aws_cli
  activate_environment "$1"
  check_profile_exists
  aws configure list
}

main "$@"
```

---

## Installation

```bash
curl -O https://example.com/aws_env.sh
chmod +x aws_env.sh
```

---

## Usage Examples

### Switch to testing environment:
```bash
./aws_env.sh testing
```

### Example Output:
```
Switched to 'testing' environment. (AWS_PROFILE=testing)

Current AWS configuration:
      Name                    Value             
      profile                testing           
      region                 us-west-2
```

### Error Example:
```bash
./aws_env.sh staging
# Error: Invalid environment. Use local, testing, or production.
```

---

## Configuration Requirements
Make sure your `~/.aws/credentials` file is set up like this:

```ini
[default]
aws_access_key_id = YOUR_DEFAULT_KEY
aws_secret_access_key = YOUR_DEFAULT_SECRET

[testing]
aws_access_key_id = YOUR_TESTING_KEY
aws_secret_access_key = YOUR_TESTING_SECRET
region = us-west-2

[production]
aws_access_key_id = YOUR_PROD_KEY
aws_secret_access_key = YOUR_PROD_SECRET
region = us-east-1
```

Set file permissions:
```bash
chmod 600 ~/.aws/credentials
```

---

## Dependencies
- AWS CLI v2+
- Bash 4.0+

---

## Troubleshooting
| Error                       | Solution                                  |
|----------------------------|-------------------------------------------|
| AWS CLI not found          | Install from https://aws.amazon.com/cli/  |
| Profile not configured     | Add profile in ~/.aws/credentials         |
| Permission denied          | Run `chmod +x aws_env.sh`                |

---

