# AI News Kubernetes Project - Makefile Template

This document provides a template for the project's Makefile, which serves as the central command interface for all project operations. The Makefile integrates all development, testing, deployment, and documentation tasks into a consistent interface.

```makefile
# ===================================================
# AI News Kubernetes Project Makefile
# ===================================================

# Configuration Variables
APP_NAME := ai-news
DOCKER_REPO := yourrepo
TAG := latest
NAMESPACE := ai-news
CHART_PATH := ./charts/ai-news

# Colors for prettier output
COLOR_RESET := \033[0m
COLOR_GREEN := \033[32m
COLOR_YELLOW := \033[33m
COLOR_BLUE := \033[34m
COLOR_CYAN := \033[36m

# ===================================================
# Development Operations
# ===================================================

.PHONY: setup
setup: ## Set up development environment
	@echo "$(COLOR_BLUE)Setting up development environment...$(COLOR_RESET)"
	python -m pip install --upgrade pip
	pip install -r requirements.txt
	pip install -r requirements-dev.txt

.PHONY: run-local
run-local: ## Run application locally
	@echo "$(COLOR_BLUE)Running application locally...$(COLOR_RESET)"
	cd src && python -m streamlit run main.py

.PHONY: format
format: ## Format code using black
	@echo "$(COLOR_BLUE)Formatting code...$(COLOR_RESET)"
	black src/

.PHONY: lint
lint: ## Lint code
	@echo "$(COLOR_BLUE)Linting code...$(COLOR_RESET)"
	flake8 src/
	pylint src/

# ===================================================
# Build Operations
# ===================================================

.PHONY: build
build: ## Build Docker image
	@echo "$(COLOR_BLUE)Building Docker image $(DOCKER_REPO)/$(APP_NAME):$(TAG)...$(COLOR_RESET)"
	docker build -t $(DOCKER_REPO)/$(APP_NAME):$(TAG) .

.PHONY: push
push: ## Push Docker image to registry
	@echo "$(COLOR_BLUE)Pushing Docker image $(DOCKER_REPO)/$(APP_NAME):$(TAG)...$(COLOR_RESET)"
	docker push $(DOCKER_REPO)/$(APP_NAME):$(TAG)

.PHONY: build-push
build-push: build push ## Build and push Docker image

# ===================================================
# Test Operations
# ===================================================

.PHONY: test
test: ## Run unit tests
	@echo "$(COLOR_BLUE)Running tests...$(COLOR_RESET)"
	pytest -xvs src/tests/

.PHONY: test-coverage
test-coverage: ## Run tests with coverage report
	@echo "$(COLOR_BLUE)Running tests with coverage...$(COLOR_RESET)"
	pytest --cov=src src/tests/

.PHONY: validate-kubernetes
validate-kubernetes: ## Validate Kubernetes manifests
	@echo "$(COLOR_BLUE)Validating Kubernetes manifests...$(COLOR_RESET)"
	kubectl validate -f kubernetes/

.PHONY: validate-helm
validate-helm: ## Validate Helm chart
	@echo "$(COLOR_BLUE)Validating Helm chart...$(COLOR_RESET)"
	helm lint $(CHART_PATH)
	helm template $(CHART_PATH) --debug

.PHONY: test-helm
test-helm: ## Run Helm tests
	@echo "$(COLOR_BLUE)Running Helm tests...$(COLOR_RESET)"
	helm test $(APP_NAME) -n $(NAMESPACE)

.PHONY: test-e2e
test-e2e: ## Run end-to-end tests
	@echo "$(COLOR_BLUE)Running end-to-end tests...$(COLOR_RESET)"
	./test-ai-news-deployment.sh

.PHONY: test-autoscaling
test-autoscaling: ## Test autoscaling functionality
	@echo "$(COLOR_BLUE)Testing autoscaling functionality...$(COLOR_RESET)"
	./test-autoscaling.sh

.PHONY: test-health-checks
test-health-checks: ## Test health check endpoints
	@echo "$(COLOR_BLUE)Testing health check endpoints...$(COLOR_RESET)"
	./debug-health-checks.sh

# ===================================================
# Kubernetes Operations
# ===================================================

.PHONY: create-namespace
create-namespace: ## Create Kubernetes namespace
	@echo "$(COLOR_BLUE)Creating namespace $(NAMESPACE)...$(COLOR_RESET)"
	kubectl create namespace $(NAMESPACE) --dry-run=client -o yaml | kubectl apply -f -

.PHONY: deploy-kubernetes
deploy-kubernetes: create-namespace ## Deploy using raw Kubernetes manifests
	@echo "$(COLOR_BLUE)Deploying with Kubernetes manifests...$(COLOR_RESET)"
	kubectl apply -f kubernetes/ -n $(NAMESPACE)

.PHONY: deploy
deploy: create-namespace ## Deploy using Helm chart
	@echo "$(COLOR_BLUE)Deploying with Helm chart...$(COLOR_RESET)"
	helm upgrade --install $(APP_NAME) $(CHART_PATH) -n $(NAMESPACE)

.PHONY: undeploy
undeploy: ## Uninstall the Helm release
	@echo "$(COLOR_BLUE)Uninstalling $(APP_NAME)...$(COLOR_RESET)"
	helm uninstall $(APP_NAME) -n $(NAMESPACE)

.PHONY: redeploy
redeploy: undeploy deploy ## Redeploy the application

.PHONY: logs
logs: ## View application logs
	@echo "$(COLOR_BLUE)Viewing application logs...$(COLOR_RESET)"
	kubectl logs -f deployment/$(APP_NAME) -n $(NAMESPACE)

.PHONY: port-forward
port-forward: ## Forward port to access the application locally
	@echo "$(COLOR_BLUE)Forwarding port 8501...$(COLOR_RESET)"
	kubectl port-forward service/$(APP_NAME) 8501:8501 -n $(NAMESPACE)

.PHONY: setup-monitoring
setup-monitoring: ## Set up monitoring stack
	@echo "$(COLOR_BLUE)Setting up monitoring stack...$(COLOR_RESET)"
	kubectl create namespace monitoring --dry-run=client -o yaml | kubectl apply -f -
	helm upgrade --install prometheus-stack kube-prometheus-stack --namespace monitoring

.PHONY: setup-logging
setup-logging: ## Set up logging stack
	@echo "$(COLOR_BLUE)Setting up logging stack...$(COLOR_RESET)"
	kubectl create namespace logging --dry-run=client -o yaml | kubectl apply -f -
	helm upgrade --install loki-stack loki-stack --namespace logging

# ===================================================
# Demo Operations
# ===================================================

.PHONY: demo-script
demo-script: ## Run demonstration script
	@echo "$(COLOR_BLUE)Running demonstration script...$(COLOR_RESET)"
	./demo-script.sh

.PHONY: demo-monitoring-logging
demo-monitoring-logging: ## Run monitoring and logging demonstration
	@echo "$(COLOR_BLUE)Running monitoring and logging demonstration...$(COLOR_RESET)"
	./demo-monitoring-logging.sh

# ===================================================
# Git Operations
# ===================================================

.PHONY: git-status
git-status: ## Show git status
	@echo "$(COLOR_BLUE)Git status:$(COLOR_RESET)"
	git status

.PHONY: commit
commit: ## Commit changes with a message
	@echo "$(COLOR_BLUE)Committing changes...$(COLOR_RESET)"
	@echo "Enter commit message (format: [Task ID] Brief description):"
	@read message; \
	git commit -m "$$message"

.PHONY: push-git
push-git: ## Push changes to GitHub
	@echo "$(COLOR_BLUE)Pushing changes to GitHub...$(COLOR_RESET)"
	git push

# ===================================================
# Documentation Operations
# ===================================================

.PHONY: update-memory-bank
update-memory-bank: ## Review and update memory bank files
	@echo "$(COLOR_BLUE)Updating memory bank files...$(COLOR_RESET)"
	@echo "Please review and update the following files:"
	@ls -1 memory-bank/
	@echo "Don't forget to commit changes after updating."

# ===================================================
# Helper targets
# ===================================================

.PHONY: help
help: ## Display this help
	@echo "$(COLOR_GREEN)AI News Kubernetes Project Makefile$(COLOR_RESET)"
	@echo "$(COLOR_YELLOW)Usage:$(COLOR_RESET) make [target]"
	@echo ""
	@echo "$(COLOR_YELLOW)Targets:$(COLOR_RESET)"
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "  $(COLOR_CYAN)%-20s$(COLOR_RESET) %s\n", $$1, $$2}'

.DEFAULT_GOAL := help
```

## Makefile Categories and Usage

The Makefile is organized into the following categories:

### Development Operations
```bash
make setup         # Set up development environment
make run-local     # Run application locally
make format        # Format code using black
make lint          # Lint code
```

### Build Operations
```bash
make build         # Build Docker image
make push          # Push Docker image to registry
make build-push    # Build and push Docker image
```

### Test Operations
```bash
make test                # Run unit tests
make test-coverage       # Run tests with coverage report
make validate-kubernetes # Validate Kubernetes manifests
make validate-helm       # Validate Helm chart
make test-helm           # Run Helm tests
make test-e2e            # Run end-to-end tests
make test-autoscaling    # Test autoscaling functionality
make test-health-checks  # Test health check endpoints
```

### Kubernetes Operations
```bash
make create-namespace   # Create Kubernetes namespace
make deploy-kubernetes  # Deploy using raw Kubernetes manifests
make deploy            # Deploy using Helm chart
make undeploy          # Uninstall the Helm release
make redeploy          # Redeploy the application
make logs              # View application logs
make port-forward      # Forward port to access the application locally
make setup-monitoring  # Set up monitoring stack
make setup-logging     # Set up logging stack
```

### Demo Operations
```bash
make demo-script              # Run demonstration script
make demo-monitoring-logging  # Run monitoring and logging demonstration
```

### Git Operations
```bash
make git-status  # Show git status
make commit      # Commit changes with a message
make push-git    # Push changes to GitHub
```

### Documentation Operations
```bash
make update-memory-bank  # Review and update memory bank files
```

### Helper Targets
```bash
make help  # Display help information
```

## Implementation Guidelines

When implementing or extending the Makefile:

1. **Maintain Organization**: Keep related commands grouped together
2. **Document All Targets**: Every target should have a helpful comment
3. **Use Variables**: Define common values as variables at the top
4. **Ensure Idempotence**: Commands should be safe to run multiple times
5. **Add Color**: Use ANSI color codes for better readability
6. **Maintain Help**: Keep the help target updated with new commands
7. **Test All Commands**: Verify each command works as expected

The Makefile serves as the central interface for all project operations, ensuring consistency and making it easy for team members to perform common tasks without remembering complex command sequences.
