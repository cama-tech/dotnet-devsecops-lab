# .NET DevSecOps Security Lab

**An intentionally vulnerable .NET 8 laboratory for DevSecOps, application security testing, ASPM experiments, and DefectDojo integration.**

This public repository is designed for developers, students, security engineers, and researchers to evaluate security scanners against controlled examples. It includes a web application, xUnit tests, intentionally insecure fixtures, and GitHub Actions workflows.

> [!WARNING]
> **Intentionally vulnerable software.** Run only on `127.0.0.1` or in an isolated, authorized environment. Do not expose it to the public Internet, deploy it to production, or use real credentials or personal data.

## What you can test

| Area | Included tools or assets |
| --- | --- |
| SAST | Semgrep; Security Code Scan |
| SCA and SBOM | Grype; dependency fixtures; SBOM workflows |
| Secret detection | Gitleaks; synthetic secrets and false-positive fixtures |
| Infrastructure as Code | Checkov; Terraform fixtures |
| Container security | Trivy; Dockerfile |
| DAST | OWASP ZAP; running web application |
| Business logic | xUnit tests and deliberately vulnerable scenarios |
| Findings management | DefectDojo integration via GitHub Actions |

The repository includes a catalog of expected findings at `aspm-csharp-lab-simple/evidence/expected-findings.json`. Scanner findings should be compared with this catalog; scanner output is not automatically proof of a confirmed vulnerability.

## Repository layout

```text
.github/
  workflows/
    semgrep.yml
    security-code-scan.yml
    gitleaks-secrets.yml
    checkov-iac.yml
    sbom-grype.yml
    trivy-container.yml
    zap-dast.yml
  semgrep/
  gitleaks/
  zap/
aspm-csharp-lab-simple/
  AspmLab.sln
  src/AspmLab/
  tests/AspmLab.Tests/
  fixtures/sca/
  fixtures/secrets/
  infra/
  evidence/expected-findings.json
  Dockerfile
  docker-compose.yml
  README.md
```

## Quick start

**Requirements:** Git and .NET 8 SDK. Docker with Docker Compose is optional.

Clone the repository (use the current URL until the GitHub repository has been renamed):

```bash
git clone https://github.com/cama-tech/aspm-csharp-lab-simple.git
cd aspm-csharp-lab-simple/aspm-csharp-lab-simple
```

Restore and run the tests:

```bash
dotnet restore AspmLab.sln
dotnet test AspmLab.sln
```

Some tests are designed to **pass when vulnerable behavior is present**. A passing test does not mean that a vulnerability has been remediated.

Run the web application locally:

```bash
dotnet run --project src/AspmLab/AspmLab.csproj --urls http://127.0.0.1:8080
```

Check its health endpoint:

```bash
curl http://127.0.0.1:8080/health
```

Expected response:

```json
{"status":"ok"}
```

Open Swagger UI at <http://127.0.0.1:8080/swagger> or the OpenAPI JSON at <http://127.0.0.1:8080/swagger/v1/swagger.json>.

### Docker alternative

From the `aspm-csharp-lab-simple/` application directory:

```bash
docker compose up --build
```

The Compose file binds port 8080 to localhost. When finished:

```bash
docker compose down --volumes
```

## Example business-logic scenarios

| ID | Intentional behavior |
| --- | --- |
| `BUSINESS-CRITICAL-001` | Unauthorized order approval |
| `BUSINESS-HIGH-001` | Access to another user's order |
| `BUSINESS-HIGH-002` | Client-side amount manipulation |
| `BUSINESS-MEDIUM-001` | Duplicate transfer processing |
| `BUSINESS-MEDIUM-002` | Transaction-limit bypass through splitting |

These scenarios are **test fixtures**, not examples of secure implementation.

## CI security scanning

The `.github/workflows/` directory contains seven security workflows covering static analysis, dependency and SBOM analysis, secrets, IaC, container scanning, and DAST. Review each workflow's event triggers, permissions, and prerequisites before enabling it in a fork. Not every workflow uploads findings on every event.

## Optional DefectDojo integration

The workflows contain an integration with the DefectDojo API, using `POST /api/v2/reimport-scan/` to submit scan reports.

Create a GitHub Actions environment named **`Dev`** under **Settings → Environments** and configure these environment secrets:

| Secret | Purpose |
| --- | --- |
| `DEFECTDOJO_URL` | URL of your DefectDojo instance |
| `DEFECTDOJO_API_TOKEN` | API token for importing results |
| `DEFECTDOJO_PRODUCT_TYPE_NAME` | Target product type |
| `DEFECTDOJO_PRODUCT_NAME` | Target product |
| `DEFECTDOJO_ENGAGEMENT_NAME` | Target engagement |

To verify the connection:

1. Set up your own DefectDojo instance and its target product/engagement.
2. Add the environment secrets without committing them to source control.
3. Run a workflow under its supported conditions.
4. Inspect the Actions logs for upload success or failure.
5. Confirm the findings appear in DefectDojo.

**Important:** An API integration in the repository does not guarantee a live DefectDojo connection. Forks do not inherit the original repository's secrets, and secrets are generally unavailable to workflows triggered by pull requests from external forks.

## Responsible use

- Use only authorized, isolated test systems and synthetic data.
- Never commit actual secrets or production scan results.
- Do not expose the intentionally vulnerable service publicly.
- Review GitHub Actions permissions before running workflows on untrusted pull requests.
- Clearly distinguish intentional vulnerabilities from accidental security defects when contributing.

## Contributing

Issues and pull requests are welcome. Please explain the test scenario, add or update automated tests, and update the expected-findings catalog when appropriate. Do not include real credentials, personal information, or attacks against third-party systems.

## License

**A license has not been selected in this README.** Before advertising unrestricted reuse, the repository owner should add a `LICENSE` file specifying the intended terms (for example, MIT, if broad reuse is desired). Public GitHub visibility alone does not grant unrestricted reuse rights.

## Maintainer

[**cama-tech**](https://github.com/cama-tech)
