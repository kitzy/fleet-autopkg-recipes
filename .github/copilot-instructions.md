# Fleet AutoPkg Recipes

This repository contains AutoPkg processors and recipes for Fleet device management platform integration. The main component is a Python processor that uploads software packages to Fleet and creates GitOps pull requests for configuration management.

Always reference these instructions first and fallback to search or bash commands only when you encounter unexpected information that does not match the info here.

## Working Effectively

### Bootstrap and Setup (macOS required for AutoPkg)
- Install Python dependencies:
  - `python3 -m pip install --upgrade pip`
  - `python3 -m pip install requests PyYAML` -- takes 10-30 seconds. NEVER CANCEL.
- Install AutoPkg (macOS only):
  - `brew install autopkg` -- takes 2-5 minutes. NEVER CANCEL. Set timeout to 10+ minutes.
  - `autopkg version`
- Configure AutoPkg repositories:
  - `autopkg repo-add https://github.com/autopkg/recipes.git` -- takes 30-60 seconds. NEVER CANCEL.
  - `autopkg repo-add https://github.com/autopkg/homebysix-recipes.git` -- takes 30-60 seconds. NEVER CANCEL.
  - `autopkg repo-add https://github.com/kitzy/fleet-autopkg-recipes.git` -- takes 30-60 seconds. NEVER CANCEL.
- Validate Python syntax:
  - `python3 -m py_compile FleetGitOpsUploader.py` -- takes < 1 second.

### Critical macOS Setup Notes
- AutoPkg ONLY works on macOS. Do not attempt to install on Linux/Windows.
- Homebrew is required: `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`
- Ensure Xcode Command Line Tools: `xcode-select --install`
- Python 3.9+ is required: `python3 --version`

### Environment Variables for Local Testing
- Export required tokens:
  - `export FLEET_GITOPS_GITHUB_TOKEN="your-github-token"`
  - `export FLEET_API_TOKEN="your-fleet-api-token"`
- Set `GIT_TERMINAL_PROMPT=0` to prevent interactive Git authentication prompts

### Testing and Validation
- Test Python dependencies: `python3 -c "import yaml, requests; print('Dependencies OK')"`
- Validate recipe YAML syntax: `python3 -c "import yaml; yaml.safe_load(open('GoogleChrome.fleet.recipe.yaml'))"`
- Test Git operations: `git status && git log --oneline -5`
- Run recipe locally (macOS only): `autopkg run GoogleChrome.fleet.recipe.yaml -k FLEET_API_TOKEN="$FLEET_API_TOKEN" -k FLEET_GITOPS_GITHUB_TOKEN="$FLEET_GITOPS_GITHUB_TOKEN"`

### File Validation
- Always validate YAML syntax when modifying recipe files
- Check Python syntax with `python3 -m py_compile FleetGitOpsUploader.py` before committing
- Test environment variable substitution in recipes
- **ALWAYS write recipes in YAML format, not XML** - This repository uses YAML recipes exclusively

## Validation Scenarios

### After Making Code Changes
- ALWAYS run `python3 -m py_compile FleetGitOpsUploader.py` to validate syntax
- Test YAML parsing: `python3 -c "import yaml; [yaml.safe_load(open(f)) for f in ['GoogleChrome.fleet.recipe.yaml', 'GithubDesktop.fleet.recipe.yaml']]"`
- Validate Git operations work correctly
- If modifying the processor, test with a sample recipe using autopkg (requires macOS)

### Manual Testing Workflow
- Create temporary directory and clone a test GitOps repository
- Set environment variables for Fleet API and GitHub tokens
- Run autopkg with sample recipe and inspect created branch/YAML changes
- Verify no sensitive data is logged or committed

## Build and Test Commands

### Syntax Validation (Linux/macOS)
- `python3 -m py_compile FleetGitOpsUploader.py` -- takes < 1 second
- `python3 -c "import yaml; yaml.safe_load(open('GoogleChrome.fleet.recipe.yaml'))"` -- takes < 1 second

### Full Recipe Testing (macOS only)
- `autopkg run GoogleChrome.fleet.recipe.yaml -v` -- takes 5-15 minutes for download/build. NEVER CANCEL. Set timeout to 30+ minutes.
- `autopkg run GithubDesktop.fleet.recipe.yaml -v` -- takes 5-15 minutes for download/build. NEVER CANCEL. Set timeout to 30+ minutes.

### Environment Testing
- Check Python version: `python3 --version` (requires 3.9+)
- Test Git: `git --version`
- Test AutoPkg: `autopkg version` (macOS only)

## Repository Structure

### Key Files
- `FleetGitOpsUploader.py` - Main AutoPkg processor for Fleet integration
- `GoogleChrome.fleet.recipe.yaml` - Example recipe for Google Chrome
- `GithubDesktop.fleet.recipe.yaml` - Example recipe for GitHub Desktop
- `README.md` - Comprehensive documentation
- `.gitignore` - Excludes Python cache and IDE files

### File Locations
- Main processor: `./FleetGitOpsUploader.py`
- Recipe files: `*.fleet.recipe.yaml`
- Documentation: `./README.md`

### Recipe Structure Understanding
AutoPkg recipes follow this pattern:
- `Description` - Human readable description
- `Identifier` - Unique identifier for the recipe
- `Input` - Variables like `NAME` for the software title
- `MinimumVersion` - Required AutoPkg version
- `ParentRecipe` - Upstream recipe that builds the package
- `Process` - Array of processors, including FleetGitOpsUploader with Arguments

Example recipe structure:
```yaml
Description: 'Builds [Software].pkg and uploads to Fleet'
Identifier: com.github.kitzy.fleet.SoftwareName
Input:
  NAME: Software Name
MinimumVersion: '2.0'
ParentRecipe: com.github.author.pkg.SoftwareName
Process:
- Arguments:
    # FleetGitOpsUploader arguments here
  Processor: FleetGitOpsUploader
```

## Common Tasks

### Creating New Recipes
- Copy an existing recipe file (e.g., `GoogleChrome.fleet.recipe.yaml`)
- Update the `ParentRecipe` to point to upstream AutoPkg recipe
- Modify processor arguments for your specific Fleet/GitOps configuration
- Test with `autopkg run YourNew.fleet.recipe.yaml -v`

### Modifying the Processor
- Edit `FleetGitOpsUploader.py`
- Validate syntax: `python3 -m py_compile FleetGitOpsUploader.py`
- Test import: `python3 -c "import sys; sys.path.insert(0, '.'); from FleetGitOpsUploader import FleetGitOpsUploader"`
- Test with sample recipe on macOS

### Local Development Workflow
1. Make changes to processor or recipes
2. Validate Python syntax
3. Test YAML parsing
4. On macOS: Run autopkg with test recipe
5. Inspect generated branch and YAML files
6. Commit changes

## Timing Expectations

- Python dependency installation: 10-30 seconds
- AutoPkg installation via brew: 2-5 minutes -- NEVER CANCEL
- AutoPkg repo addition: 30-60 seconds each -- NEVER CANCEL
- Recipe execution (full build): 5-15 minutes -- NEVER CANCEL. Set timeout to 30+ minutes
- Python syntax validation: < 1 second (measured: 0 seconds)
- YAML validation: < 1 second (measured: 0 seconds)
- Git operations: < 5 seconds (measured: 0 seconds)
- Batch YAML validation: < 1 second (measured: 0 seconds)
- Environment variable setup: < 1 second

## Platform Requirements

### macOS (Full Functionality)
- Required for AutoPkg execution
- Homebrew for AutoPkg installation
- All features available

### Linux/Windows (Limited)
- Can validate Python syntax and YAML
- Can test Git operations
- Cannot run AutoPkg recipes
- Useful for CI/CD validation

## Security Notes

- Never commit tokens or secrets to repository
- Use environment variables for sensitive data
- Set `GIT_TERMINAL_PROMPT=0` to prevent interactive authentication
- Tokens should have minimal required permissions
- Rotate Fleet API tokens periodically

## Troubleshooting

### Common Issues
- **AutoPkg not found**: Install via `brew install autopkg` (macOS only)
- **Python import errors**: Install dependencies with `pip install requests PyYAML`
- **YAML syntax errors**: Validate with `python3 -c "import yaml; yaml.safe_load(open('file.yaml'))"`
- **Git authentication**: Set `FLEET_GITOPS_GITHUB_TOKEN` environment variable
- **Fleet API errors**: Verify `FLEET_API_TOKEN` and Fleet server URL

### Debug Commands
- `autopkg version` - Check AutoPkg installation
- `python3 --version` - Verify Python 3.9+
- `git --version` - Check Git availability
- `python3 -c "import yaml, requests"` - Test dependencies

## Integration Details

### Fleet Integration
- Uploads .pkg files to Fleet software management
- Creates software YAML configurations
- Manages team-based software deployment

### GitOps Workflow
- Creates feature branches for software updates
- Updates team YAML configurations
- Opens pull requests for review
- Maintains idempotent operations

### GitHub Actions
- Runs on `macos-latest` runners
- Requires `contents: write` and `pull-requests: write` permissions
- Uses scheduled triggers for automated updates
- Supports manual dispatch for testing

## Common Command Outputs

### Repository Structure
```
ls -la
total 68
drwxr-xr-x 3 runner runner  4096 Sep 17 22:57 .
drwxr-xr-x 3 runner runner  4096 Sep 17 22:57 ..
drwxrwxr-x 7 runner runner  4096 Sep 17 22:57 .git
-rw-rw-r-- 1 runner runner   267 Sep 17 22:57 .gitignore
-rw-rw-r-- 1 runner runner 27406 Sep 17 22:57 FleetGitOpsUploader.py
-rw-rw-r-- 1 runner runner  1114 Sep 17 22:57 GithubDesktop.fleet.recipe.yaml
-rw-rw-r-- 1 runner runner  1187 Sep 17 22:57 GoogleChrome.fleet.recipe.yaml
-rw-rw-r-- 1 runner runner 13740 Sep 17 22:57 README.md
```

### Python Dependencies Check
```
python3 -c "import yaml, requests; print('Dependencies OK')"
Dependencies OK
```

### Git Status (Clean)
```
git status
On branch main
Your branch is up to date with 'origin/main'.
nothing to commit, working tree clean
```

### Environment Variables Setup
```
export FLEET_GITOPS_GITHUB_TOKEN="your-token"
export FLEET_API_TOKEN="your-fleet-token"
export GIT_TERMINAL_PROMPT=0
```

## Quick Reference Commands

### Immediate Validation (Run These First)
```bash
python3 --version                                    # Should be 3.9+
python3 -c "import yaml, requests"                   # Test dependencies
python3 -m py_compile FleetGitOpsUploader.py         # Validate syntax
git --version                                        # Check Git availability
```

### Full Validation Sequence
```bash
# Environment setup
export FLEET_GITOPS_GITHUB_TOKEN="your-token"
export FLEET_API_TOKEN="your-fleet-token"
export GIT_TERMINAL_PROMPT=0

# Validate all components
python3 -m py_compile FleetGitOpsUploader.py
python3 -c "import yaml; [yaml.safe_load(open(f)) for f in ['GoogleChrome.fleet.recipe.yaml', 'GithubDesktop.fleet.recipe.yaml']]"
git status && git log --oneline -5
```

## References

- [AutoPkg Documentation](https://github.com/autopkg/autopkg/wiki) - Official AutoPkg wiki and documentation
- [AutoPkg Source Code](https://github.com/autopkg/autopkg) - AutoPkg main repository and source code
- Fleet GitOps YAML software docs: https://fleetdm.com/docs/configuration/yaml-files#software
- Fleet API documentation for software management integration