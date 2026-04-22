# OIDC vs IAM Users in CI/CD (GitHub Actions + AWS)

## 📌 What is OIDC?

**OIDC (OpenID Connect)** is an authentication protocol that allows external systems (like GitHub Actions) to securely access AWS resources **without storing credentials**.

Instead of using long-lived credentials (Access Key / Secret Key), OIDC works using:

- Trusted identity (GitHub)
- IAM Role in AWS
- Temporary credentials (short-lived)

---

## 🔄 Traditional Approach (IAM User)

### Flow:

GitHub Actions → AWS Access Keys → IAM User → AWS

### How it works:

1. You create an IAM User in AWS
2. Generate:
   - `AWS_ACCESS_KEY_ID`
   - `AWS_SECRET_ACCESS_KEY`
3. Store them in GitHub Secrets
4. GitHub Actions uses them to authenticate with AWS

---

## ❌ Problems with IAM Users

### 1. Long-lived credentials
- Access keys don't expire automatically
- If leaked → attacker gets full access

### 2. Secrets stored in GitHub
- Even if encrypted, still a risk
- Must be manually rotated

### 3. Operational overhead
- Key rotation
- Secret management
- Risk of misuse

---

## ✅ Modern Approach (OIDC + IAM Role)

### Flow:

GitHub Actions → OIDC → Assume IAM Role → Temporary Credentials → AWS

---

## ⚙️ How OIDC Works

1. GitHub Actions requests a token from GitHub's OIDC provider
2. AWS verifies:
   - Repository
   - Branch
   - Workflow identity
3. If trusted → AWS allows:
   - `AssumeRole`
4. AWS returns **temporary credentials**

---

## 🔐 Why OIDC is Better

### 1. No stored secrets

- ❌ IAM User: Secrets stored in GitHub
- ✅ OIDC: No secrets at all

---

### 2. Temporary credentials

- Credentials expire automatically
- Even if stolen → limited time window

---

### 3. Least privilege access

- IAM Role can be tightly scoped
- Only specific repos / branches can assume it

---

### 4. More secure by design

Attacker now needs:

- Access to your GitHub repo **AND**
- Ability to run workflows

Not just stolen credentials.

---

### 5. Production best practice

- Used in real companies
- Recommended by AWS
- Industry standard for CI/CD

---

## 🧠 Key Concept

> OIDC does NOT replace IAM  
> It changes **how authentication happens**

- IAM User → static identity (bad for CI/CD)
- IAM Role → dynamic identity (best for CI/CD)

---

## 🔄 Comparison

| Feature                | IAM User            | OIDC + IAM Role        |
|----------------------|--------------------|------------------------|
| Credentials          | Static             | Temporary              |
| Stored in GitHub     | Yes                | No                     |
| Rotation needed      | Yes                | No                     |
| Security level       | Medium             | High                   |
| Recommended for CI/CD| ❌ No              | ✅ Yes                 |

---

## 🧪 Real Use Case (Your Project)

In your pipeline:

1. GitHub Actions authenticates using OIDC
2. Assumes IAM Role:
   - For ECR push
   - For deployment
3. AWS returns temporary credentials
4. Pipeline:
   - Push image to ECR
   - Deploy to Kubernetes

---

## 🚀 Conclusion

Using OIDC instead of IAM Users:

- Eliminates credential management
- Improves security significantly
- Matches real-world DevOps practices

👉 This is the **correct production-grade approach** for CI/CD pipelines.

---

## 📚 References

- AWS OIDC Documentation
- GitHub Actions OIDC Docs
