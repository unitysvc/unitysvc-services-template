# UnitySVC Services Template

**A template repository for digital service providers to publish their services on the UnitySVC platform.**

Use this template to create your own service provider repository with automated validation and publishing workflows.

## 🚀 Quick Start

### 1. Create Your Repository from Template

1. Click the **"Use this template"** button at the top of this repository
2. Choose a name for your repository (e.g., `unitysvc-services-yourcompany`)
3. Clone your new repository locally

### 2. Customize Your Data

Replace the example data in the `data/` directory with your actual service information:

**Required changes:**

- [ ] Update `data/provider.toml` with your company information
- [ ] Update `data/README.md` with your company description
- [ ] Update `data/docs/` with your actual documentation and code examples
- [ ] Replace example services in `data/services/` with your actual services

**Recommended naming:**

- Provider name: Use your company name (e.g., "acme-corp")
- Service names: Use descriptive names (e.g., "gpt-4-turbo", "claude-3-opus")
- Keep names lowercase with hyphens (e.g., "my-service-name")

### 3. Update Repository Title and Description

Edit the top of this README.md:

```markdown
# UnitySVC Services - Your Company Name

This repository hosts service data for Your Company's digital services on the UnitySVC platform.
```

## 📁 Repository Structure

```
data/
└── your-provider-name/              # Directory name must match provider name
    ├── provider.toml                # Your provider metadata
    ├── README.md                    # Your provider documentation
    ├── docs/                        # Code examples and descriptions
    │   ├── code-example.py
    │   ├── code-example.js
    │   ├── code-example.sh
    │   └── description.md
    └── services/                    # Service definitions
        └── your-service-name/
            ├── service.json         # Service offering (technical specs)
            └── svcreseller.json     # Service listing (user-facing info)
```

**Important**: The directory name under `data/` must match the `name` field in `provider.toml`.

## 🛠️ Setup

### Install unitysvc-services

The `unitysvc-services` package provides CLI tools for validating and publishing service data:

```bash
pip install unitysvc-services
```

### Install Pre-commit Hooks (Optional but Recommended)

Pre-commit hooks automatically validate and format your data files before each commit:

```bash
# Install pre-commit (choose one method)
pip install pre-commit           # Using pip
brew install pre-commit           # On macOS using Homebrew
conda install -c conda-forge pre-commit  # Using conda

# Install the git hooks in your repository
cd /path/to/unitysvc-services-yourcompany
pre-commit install

# Test the hooks by running them manually on all files
pre-commit run --all-files
```

The pre-commit hooks will automatically:

- Format JSON files with 2-space indentation
- Validate JSON and TOML syntax
- Remove trailing whitespace
- Fix end-of-file formatting
- Validate data files against schemas

**Note**: You can also use `unitysvc_services format data` to format files manually without installing pre-commit hooks.

**Troubleshooting**: If the validate hook fails, ensure `unitysvc-services` is installed in your current Python environment:

```bash
pip install unitysvc-services
# Or if using a virtual environment, activate it first
source venv/bin/activate  # On Linux/macOS
# or
venv\Scripts\activate     # On Windows
```

## 📝 Development Workflow

### Create a New Service

```bash
# Create a new service offering
unitysvc_services init-offering my-new-service

# Create a new service listing
unitysvc_services init-listing my-new-service

# Or copy from an existing service
unitysvc_services init-offering my-new-service --source data/services/example-service
```

### Format Data Files

Before committing, format your data files to match pre-commit requirements:

```bash
# Format all files in the data directory
unitysvc_services format data

# Check if files are properly formatted without modifying them
unitysvc_services format data --check

# The formatter:
# - Formats JSON files with 2-space indentation and sorted keys
# - Removes trailing whitespace
# - Ensures files end with a newline
# - Reports changes made to each file
```

### Validate Data Locally

Before committing changes, validate your data files:

```bash
# Validate all files in the data directory
unitysvc_services validate data

# The validator checks:
# - Schema compliance
# - File references exist
# - Jinja2 template syntax
# - Directory name consistency
# - URL formats
```

### Publish Data Manually

You can manually publish data to a UnitySVC backend:

```bash
# Set environment variables
export UNITYSVC_BACKEND_URL="https://api.unitysvc.com"
export UNITYSVC_API_KEY="your-api-key"

# Publish all data
unitysvc_services publish-providers data
unitysvc_services publish-offerings data
unitysvc_services publish-listings data

# Or publish individual files
unitysvc_services publish-provider data/provider.toml
unitysvc_services publish-offering data/services/my-service/service.json
unitysvc_services publish-listing data/services/my-service/svcreseller.json
```

### Populate Services (Optional)

If your provider supports automated service updates, you can configure a `services_populator` section in provider.toml:

```toml
[services_populator]
command = ['scripts/update_services.py', '--force']
```

Then run the populate command:

```bash
# Populate services for all providers
unitysvc_services populate-services data

# Populate for specific provider
unitysvc_services populate-services data --provider my-provider

# Dry-run to preview
unitysvc_services populate-services data --dry-run
```

The command will automatically:

- Use environment variables from `provider_access_info` (api_key, api_endpoint, etc.)
- Execute your custom update script
- Generate/update service files in your data directory

## 🤖 GitHub Actions

This repository includes automated workflows:

### Validate Data

Runs on every pull request and push to main:

- Validates all data files against schemas
- Ensures data integrity before merging

### Format Check

Runs on every pull request and push to main:

- Checks JSON/TOML formatting
- Ensures consistent code style

### Publish Data

Runs automatically when changes are merged to `main`:

- Publishes providers, service offerings, and service listings
- Updates the configured UnitySVC backend

## 🔐 GitHub Secrets Configuration

To enable automatic publishing when changes are merged to `main`, configure the following GitHub secrets:

### Step 1: Navigate to Repository Settings

1. Go to your repository on GitHub
2. Click **Settings** tab
3. In the left sidebar, click **Secrets and variables** → **Actions**

### Step 2: Add Secrets

Click **New repository secret** and add the following secrets:

#### `UNITYSVC_BACKEND_URL`

- **Description**: The UnitySVC backend API URL
- **Example values**:
  - Production: `https://api.unitysvc.com`
  - Staging: `https://staging.unitysvc.com`
  - Development: `https://main.devel.unitysvc.com`

#### `UNITYSVC_API_KEY`

- **Description**: API key for authenticating with the UnitySVC backend
- **How to obtain**:
  1. Log in to the UnitySVC platform (username should match the "seller" in service listings)
  2. Navigate to **Settings** → **API Keys**
  3. Generate a new API key for your organization
  4. Copy the key (you won't be able to see it again)

### Step 3: Verify Configuration

After adding the secrets:

1. Make a change to a file in the `data/` directory
2. Create a pull request
3. Merge the pull request to `main`
4. Check the **Actions** tab to see the publishing workflow run
5. Verify that data appears on your UnitySVC backend

## 📄 File Formats

Both JSON and TOML formats are supported for service data:

### JSON Format

```json
{
  "schema": "service_v1",
  "name": "my-service",
  "service_type": "llm",
  "display_name": "My Service",
  "description": "A high-performance digital service"
}
```

### TOML Format

```toml
schema = "service_v1"
name = "my-service"
service_type = "llm"
display_name = "My Service"
description = "A high-performance digital service"
```

## 📚 Documentation

For detailed information on data models and schemas:

- **Provider Model**: Provider metadata, contact info, and branding
- **Service Offering Model**: Technical specifications, pricing, API endpoints
- **Service Listing Model**: User-facing information, documentation, code examples

Refer to the [UnitySVC Services SDK documentation](https://github.com/unitysvc/unitysvc-services) for full schema details.

## 🔄 Contributing

1. Create a new branch for your changes
2. Make your changes to files in the `data/` directory
3. Run `unitysvc_services validate data` to check your changes
4. Commit your changes (pre-commit hooks will run automatically)
5. Push your branch and create a pull request
6. Once approved and merged, data will be automatically published

## 💡 Tips

- **Keep service names consistent**: Use the same name across `service.json`, `svcreseller.json`, and directory names
- **Test locally first**: Always run `unitysvc_services validate data` before pushing
- **Use pre-commit hooks**: They catch formatting and validation errors early
- **Document your services**: Good documentation helps users understand and adopt your services
- **Version your services**: Consider including version numbers in service names (e.g., `my-service-v2`)

## 📞 Support

For issues or questions:

- **UnitySVC Services SDK**: https://github.com/unitysvc/unitysvc-services
- **UnitySVC Documentation**: https://docs.unitysvc.com
- **Issues**: Open an issue in this repository

## 📜 License

This template is provided under the MIT License. Service data you add is subject to your own licensing terms.
