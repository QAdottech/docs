# QA.tech GitHub Action

This action integrates your GitHub workflow with QA.tech, triggering test runs automatically when configured events occur.

## Usage

```yaml
name: QA.tech Tests
on:
  push:
    branches:
      - main

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: QAdottech/run-action@v2
        with:
          project_id: 'your-project-id'
          api_token: ${{ secrets.QATECH_API_TOKEN }}
          test_plan_short_id: 'jgbinp'
```

## Inputs

| Input                 | Description                                                | Required | Default               |
| --------------------- | ---------------------------------------------------------- | -------- | --------------------- |
| `project_id`          | Your QA.tech project ID                                    | Yes      | -                     |
| `api_token`           | QA.tech API token                                          | Yes      | -                     |
| `api_url`             | Custom API URL if needed                                   | No       | https://app.qa.tech |
| `test_plan_short_id`  | Test plan short ID to run                                  | No       | -                     |
| `blocking`            | Enables blocking mode to wait for the test run to complete | No       | false                 |
| `applications_config` | JSON string containing application environment overrides   | No       | -                     |

You can find your project ID and generate an API token in your [QA.tech project settings](https://app.qa.tech/dashboard/current-project/settings/integrations).

## Outputs

| Output       | Description                                  |
| ------------ | -------------------------------------------- |
| `runId`      | The ID of the created test run               |
| `runShortId` | A short ID for the test run                  |
| `success`    | Boolean indicating if the run was successful |

## Test Plan

Specify which test plan to run by providing its ID in the test_plan_short_id input. To run multiple test plans, simply use the GitHub Action multiple times in your workflow.

For example:

```yaml
- uses: QAdottech/run-action@v2
  with:
    project_id: 'your-project-id'
    api_token: ${{ secrets.QATECH_API_TOKEN }}
    test_plan_short_id: 'jgbinp'
```

## Blocking

The action supports a blocking mode that will wait for the test run to complete before proceeding. When enabled, the action will:

1. Create the test run
2. Poll the run status until completion
3. Set additional outputs with the final status and result
4. Fail the GitHub Action if the test run fails

To enable blocking mode, set the `blocking` input to `true`:

```yaml
- uses: QAdottech/run-action@v2
  with:
    project_id: 'your-project-id'
    api_token: ${{ secrets.QATECH_API_TOKEN }}
    blocking: true
```

When blocking is enabled, the action provides additional outputs:

- `run_status`: The final status of the run (INITIATED, RUNNING, COMPLETED, ERROR, or CANCELLED)
- `run_result`: The test execution result (PASSED, FAILED, or SKIPPED)

## Application Environment Overrides

You can override application environments for specific runs using the `applications_config` input. This is useful for testing against preview deployments or specific environment URLs.

The input expects a JSON string with the following format:

```json
{
  "applications": {
    "appId": {
      "environment": {
        "url": "https://preview-123.vercel.app",
        "name": "Preview Environment"
      }
    }
  }
}
```

### Example Usage

```yaml
- uses: QAdottech/run-action@v2
  with:
    project_id: 'your-project-id'
    api_token: ${{ secrets.QATECH_API_TOKEN }}
    test_plan_short_id: 'jgbinp'
    applications_config: |
      {
        "applications": {
          "short-id-1": {
            "environment": {
              "url": "https://preview-123-hackoffice.vercel.app"
            }
          }
        }
      }
```

You can override multiple applications in a single run:

> **Note**: Check on app.qa.tech under Test Plans → API Integration to see which applications are connected to your test plan.

```yaml
applications_config: |
  {
    "applications": {
      "short-id-1": {
        "environment": {
          "url": "https://preview-123-hackoffice.vercel.app",
          "name": "PR-123"
        }
      },
      "short-id-2": {
        "environment": {
          "url": "https://preview-123-saas.vercel.app",
          "name": "PR-123"
        }
      }
    }
  }
```

## Complete Example with Preview Deployments

Here's a complete example that deploys preview environments and then tests them:

```yaml
name: Test Preview Deployment
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  deploy:
    runs-on: ubuntu-latest
    outputs:
      frontend_url: ${{ steps.deploy_frontend.outputs.url }}
      backend_url: ${{ steps.deploy_backend.outputs.url }}
    steps:
      # Your deployment steps here
      - name: Deploy Frontend
        id: deploy_frontend
        # Your deployment logic here

      - name: Deploy Backend
        id: deploy_backend
        # Your deployment logic here

  test:
    runs-on: ubuntu-latest
    needs: deploy
    steps:
      - uses: QAdottech/run-action@v2
        with:
          project_id: ${{ secrets.QATECH_PROJECT_ID }}
          api_token: ${{ secrets.QATECH_API_TOKEN }}
          test_plan_short_id: 'your-test-plan-id'
          applications_config: |
            {
              "applications": {
                "frontend-app-id": {
                  "environment": {
                    "url": "${{ needs.deploy.outputs.frontend_url }}",
                    "name": "PR-${{ github.event.number }}-Frontend"
                  }
                },
                "backend-app-id": {
                  "environment": {
                    "url": "${{ needs.deploy.outputs.backend_url }}",
                    "name": "PR-${{ github.event.number }}-Backend"
                  }
                }
              }
            }
```

## Setup

1. **Get your Project ID**: Find it in your QA.tech project settings
2. **Generate API Token**: Go to Project Settings → Integrations → API
3. **Add GitHub Secrets**:
   - `QATECH_PROJECT_ID`: Your project ID
   - `QATECH_API_TOKEN`: Your generated API token

## API Integration

If you prefer direct API calls instead of using the GitHub Action, you can use cURL:

### Basic Usage

```bash
curl -X POST \
  -H "Authorization: Bearer $QATECH_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"testPlanShortId": "your-test-plan-id"}' \
  https://app.qa.tech/api/projects/$QATECH_PROJECT_ID/runs
```

### With Application Environment Overrides

```bash
curl -X POST \
  -H "Authorization: Bearer $QATECH_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "testPlanShortId": "your-test-plan-id",
    "applications": {
      "app-short-id": {
        "environment": {
          "url": "https://preview-123.vercel.app",
          "name": "Preview Environment"
        }
      }
    }
  }' \
  https://app.qa.tech/api/projects/$QATECH_PROJECT_ID/runs
```
