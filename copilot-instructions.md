# DEVOPS OPERATIONAL GUIDELINES

## PRIME DIRECTIVE
- Infrastructure as Code (IaC) is the foundation of all operations
- Every change must be version controlled and peer-reviewed
- Automate everything that can be automated
- Maintain comprehensive documentation and runbooks
- Follow the principle of least privilege for all access
- If you need to wait for something in the command line and need to use sleep, use 'caffeinate -t <time>' to prevent the system from sleeping during long operations

## CORE PRINCIPLES

### DRY (Don't Repeat Yourself)
- Create reusable modules and roles
- Centralize configuration management
- Use templating for similar configurations
- Implement shared libraries and common functions
- Avoid duplicating infrastructure patterns

### SOLID Principles for Infrastructure
- **Single Responsibility**: Each module/role should have one clear purpose
- **Open/Closed**: Infrastructure should be open for extension, closed for modification
- **Liskov Substitution**: Components should be interchangeable within their abstraction
- **Interface Segregation**: Create focused, minimal interfaces between components
- **Dependency Inversion**: Depend on abstractions, not concrete implementations

## TERRAFORM BEST PRACTICES

### PROJECT STRUCTURE
```
terraform/
├── environments/
│   ├── dev/
│   ├── staging/
│   └── prod/
├── modules/
│   ├── compute/
│   ├── networking/
│   ├── storage/
│   └── security/
├── shared/
│   ├── variables/
│   ├── data-sources/
│   └── providers/
├── policies/
│   ├── security/
│   └── compliance/
└── scripts/
    ├── deploy/
    └── validate/
```

### TERRAFORM STANDARDS
- **Version Management**:
  - Pin Terraform version in `required_version`
  - Pin provider versions with `~>` operator for patch updates
  - Use `.terraform-version` file for tfenv compatibility

- **State Management**:
  - Always use remote state (S3, Terraform Cloud, etc.)
  - Enable state locking (DynamoDB for S3)
  - Implement state encryption at rest
  - Use separate state files per environment
  - Never commit state files to version control

- **Module Design**:
  - Create focused, reusable modules
  - Use semantic versioning for module releases
  - Implement comprehensive variable validation
  - Provide clear outputs for module consumers
  - Include complete documentation and examples

- **Resource Naming**:
  - Use consistent naming conventions across all resources
  - Include environment, project, and resource type in names
  - Format: `{project}-{environment}-{resource-type}-{identifier}`
  - Example: `myapp-prod-web-alb-01`

- **Variable Management**:
  - Define variables with appropriate types and descriptions
  - Use validation rules for input parameters
  - Implement default values where sensible
  - Group related variables logically
  - Use `.tfvars` files for environment-specific values

### TERRAFORM WORKFLOW
1. **Planning Phase**:
   - Run `terraform fmt` to ensure consistent formatting
   - Execute `terraform validate` for syntax checking
   - Perform `terraform plan` and review all changes
   - Use `terraform plan -out=plan.out` for applying exact plans

2. **Security Scanning**:
   - Run tools like `tfsec`, `checkov`, or `terrascan`
   - Implement policy as code with Sentinel or OPA
   - Scan for secrets and credentials in code
   - Validate compliance with organizational policies

3. **Deployment Process**:
   - Use CI/CD pipelines for all deployments
   - Implement approval workflows for production changes
   - Maintain deployment logs and audit trails
   - Use blue-green or canary deployment strategies

## ANSIBLE BEST PRACTICES

### PROJECT STRUCTURE
```
ansible/
├── inventories/
│   ├── dev/
│   ├── staging/
│   └── prod/
├── group_vars/
│   ├── all/
│   ├── webservers/
│   └── databases/
├── host_vars/
├── roles/
│   ├── common/
│   ├── webserver/
│   ├── database/
│   └── monitoring/
├── playbooks/
│   ├── site.yml
│   ├── webservers.yml
│   └── databases.yml
├── files/
├── templates/
├── vault/
└── scripts/
```

### ANSIBLE STANDARDS
- **Role Development**:
  - Follow the standard role directory structure
  - Use `ansible-galaxy init` to create role skeletons
  - Implement comprehensive testing with Molecule
  - Create idempotent tasks that can run multiple times safely
  - Use handlers for service restarts and notifications

- **Variable Management**:
  - Use descriptive variable names with consistent naming
  - Implement variable precedence understanding
  - Store sensitive data in Ansible Vault
  - Use group_vars and host_vars appropriately
  - Avoid hardcoding values in tasks

- **Task Design**:
  - Write descriptive task names
  - Use appropriate modules (avoid shell/command when possible)
  - Implement proper error handling and validation
  - Use tags for selective execution
  - Include changed_when and failed_when conditions

- **Inventory Management**:
  - Use dynamic inventories when possible
  - Implement proper host grouping strategies
  - Include all necessary host variables
  - Use consistent naming conventions
  - Maintain separate inventories per environment

### ANSIBLE SECURITY
- **Vault Management**:
  - Encrypt all sensitive data with ansible-vault
  - Use separate vault files for different environments
  - Implement proper vault password management
  - Never commit unencrypted secrets to version control
  - Use vault IDs for multi-vault scenarios

- **Privilege Management**:
  - Use become/sudo only when necessary
  - Implement proper user and group management
  - Follow principle of least privilege
  - Use SSH keys instead of passwords
  - Regularly rotate credentials and keys

## CI/CD PIPELINE REQUIREMENTS

### PIPELINE STRUCTURE
```yaml
stages:
  - validate
  - security-scan
  - test
  - plan
  - deploy
  - verify
  - cleanup
```

### VALIDATION STAGE
- Syntax checking (terraform validate, ansible-lint)
- Code formatting verification
- Documentation generation and updates
- Dependency checking and updates

### SECURITY SCANNING
- Infrastructure security scanning (tfsec, checkov)
- Secret detection (git-secrets, truffleHog)
- Compliance policy validation
- Vulnerability assessment

### TESTING REQUIREMENTS
- Unit tests for Terraform modules
- Integration tests with Terratest
- Ansible playbook testing with Molecule
- Infrastructure testing with InSpec or Goss
- Performance and load testing where applicable

### DEPLOYMENT STANDARDS
- Implement approval gates for production
- Use deployment slots or blue-green strategies
- Maintain rollback capabilities
- Include health checks and monitoring
- Generate deployment reports and notifications

## MONITORING AND OBSERVABILITY

### INFRASTRUCTURE MONITORING
- Implement comprehensive metric collection
- Use centralized logging (ELK, Splunk, etc.)
- Create meaningful dashboards and alerts
- Monitor infrastructure drift and compliance
- Implement automated remediation where possible

### APPLICATION MONITORING
- Implement distributed tracing
- Monitor application performance metrics
- Create SLIs and SLOs for critical services
- Use synthetic monitoring for user experience
- Implement chaos engineering practices

## SECURITY BEST PRACTICES

### ACCESS CONTROL
- Implement role-based access control (RBAC)
- Use multi-factor authentication (MFA)
- Regularly audit access permissions
- Implement just-in-time access where possible
- Maintain audit logs for all access

### SECRETS MANAGEMENT
- Use dedicated secrets management tools (Vault, AWS Secrets Manager)
- Implement secret rotation policies
- Never store secrets in plain text
- Use service accounts with minimal permissions
- Implement secret scanning in CI/CD pipelines

### NETWORK SECURITY
- Implement network segmentation
- Use VPNs for remote access
- Implement WAF and DDoS protection
- Regular security assessments and penetration testing
- Maintain network topology documentation

## DISASTER RECOVERY AND BACKUP

### BACKUP STRATEGIES
- Implement automated backup procedures
- Test backup restoration regularly
- Maintain off-site backup copies
- Document recovery procedures
- Implement point-in-time recovery capabilities

### DISASTER RECOVERY
- Maintain disaster recovery runbooks
- Implement cross-region redundancy
- Regular disaster recovery testing
- Define RTO and RPO requirements
- Maintain communication plans for incidents

## DOCUMENTATION REQUIREMENTS

### MANDATORY DOCUMENTATION
- Architecture diagrams and documentation
- Runbooks for common operations
- Incident response procedures
- Change management processes
- API documentation for all services

### DOCUMENTATION STANDARDS
- Use Markdown for all documentation
- Maintain documentation in version control
- Include code examples and usage patterns
- Keep documentation up-to-date with changes
- Use diagrams-as-code tools (Mermaid, PlantUML)

## CHANGE MANAGEMENT

### CHANGE PROCESS
1. **Planning**: Document the change and impact assessment
2. **Review**: Peer review of all changes
3. **Testing**: Comprehensive testing in non-production environments
4. **Approval**: Stakeholder approval for production changes
5. **Implementation**: Controlled deployment with monitoring
6. **Verification**: Post-deployment validation and testing
7. **Documentation**: Update documentation and knowledge base

### EMERGENCY PROCEDURES
- Define emergency change procedures
- Maintain emergency contacts and escalation paths
- Implement rapid rollback capabilities
- Document all emergency changes retroactively
- Conduct post-incident reviews and improvements

## COMPLIANCE AND GOVERNANCE

### POLICY ENFORCEMENT
- Implement policy as code
- Regular compliance auditing
- Automated compliance reporting
- Remediation tracking and management
- Staff training and awareness programs

### GOVERNANCE FRAMEWORKS
- Implement appropriate governance frameworks (NIST, ISO 27001, etc.)
- Regular risk assessments
- Maintain compliance documentation
- Implement continuous compliance monitoring
- Regular governance reviews and updates