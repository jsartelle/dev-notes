---
{"dg-publish":true,"dg-path":"Notes/Terraform vs. CDK.md","permalink":"/notes/terraform-vs-cdk/"}
---


- both declarative IaC (infrastructure as code) tools
- Terraform:
    - **provider-agnostic**
    - **language-specific**
    - industry standard
    - Terraform Cloud lets you easily deploy Terraform templates with a full CI/CD process
- CDK:
    - **provider-specific** (AWS)
    - **language-agnostic**
        - supports JS, TS, Python, Java, C#, Go
    - generates CloudFormation templates, can optionally generate Terraform templates
