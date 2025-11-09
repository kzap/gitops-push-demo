# GitHub Actions Workflows

## demo-gitops-push.yaml

This workflow demonstrates how to use the [gitops-push action](https://github.com/kzap/gitops-push) to deploy applications to a GitOps repository using either Helm or Kustomize.

### Trigger

This workflow uses `workflow_dispatch` for manual triggering through the GitHub UI.

### Inputs

- **application-name** (choice, required): Select the application to deploy
  - Options: `echo-service`
  
- **environment** (environment, required): The target environment
  - Must match environments configured in your GitHub repository settings
  - Common values: `dev`, `stage`, `production`
  
- **deployment-method** (choice, required): Choose deployment method
  - Options: `helm`, `kustomize`
  - Default: `helm`

### Prerequisites

#### 1. GitHub Secrets

Create the following secret in your repository:

- **GITOPS_TOKEN**: A GitHub Personal Access Token (PAT) with `contents: write` permission for the GitOps repository
  - Go to: Settings → Secrets and variables → Actions → New repository secret
  - Token should have write access to your GitOps repository
  - Consider using a dedicated bot account or GitHub App

#### 2. GitHub Variables

Create the following variable in your repository:

- **GITOPS_REPOSITORY**: The name of your GitOps repository (format: `owner/repo`)
  - Go to: Settings → Secrets and variables → Actions → Variables tab → New repository variable
  - Example: `myorg/gitops-repo`

#### 3. GitHub Environments

Configure environments in your repository settings:

- Go to: Settings → Environments
- Create environments: `dev`, `stage`, `production`
- Optionally add protection rules and reviewers for production

### Usage

1. Go to the **Actions** tab in your GitHub repository
2. Select **Demo GitOps Push** workflow from the left sidebar
3. Click **Run workflow** button
4. Fill in the inputs:
   - Select application: `echo-service`
   - Select environment: `dev`, `stage`, or `production`
   - Select deployment method: `helm` or `kustomize`
5. Click **Run workflow** to start the deployment

### How It Works

#### Helm Deployment

When using Helm deployment method:
1. Checks out the service repository
2. Pushes the Helm chart from `./echo-service/charts/echo-service/` to the GitOps repository
3. Uses environment-specific values from `values/<environment>/values.yaml`
4. Creates an ArgoCD ApplicationSet in the GitOps repository

#### Kustomize Deployment

When using Kustomize deployment method:
1. Checks out the service repository
2. Pushes the Kustomize manifests from `./echo-service/kustomize/overlays/<environment>/` to the GitOps repository
3. Creates an ArgoCD ApplicationSet in the GitOps repository

### GitOps Repository Structure

After running the workflow, your GitOps repository will contain:

```
gitops-repo/
└── deployments/
    ├── application-sets/
    │   └── echo-service-<environment>.yaml  # ArgoCD ApplicationSet
    └── applications/
        └── echo-service/
            └── <environment>/
                └── [manifests from your chosen deployment method]
```

### Example Workflow Run

```yaml
Application: echo-service
Environment: production
Method: helm
GitOps Repository: myorg/gitops-repo
```

This will:
1. Copy the Helm chart to `deployments/applications/echo-service/production/`
2. Create ArgoCD ApplicationSet at `deployments/application-sets/echo-service-production.yaml`
3. ArgoCD will detect the changes and sync the application to the cluster

### Troubleshooting

**Error: "GITOPS_TOKEN secret not found"**
- Ensure you've created the `GITOPS_TOKEN` secret with proper permissions

**Error: "Repository not found"**
- Verify the `GITOPS_REPOSITORY` variable is set correctly
- Ensure the token has access to the GitOps repository

**Error: "Environment not found"**
- Create the environment in repository Settings → Environments

**Deployment not happening**
- Check ArgoCD is running and configured to watch the GitOps repository
- Verify the ArgoCD ApplicationSet was created in the GitOps repository
- Check ArgoCD logs for sync errors

### References

- [gitops-push Action](https://github.com/kzap/gitops-push)
- [GitHub Actions workflow_dispatch](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#workflow_dispatch)
- [ArgoCD ApplicationSets](https://argo-cd.readthedocs.io/en/stable/user-guide/application-set/)

