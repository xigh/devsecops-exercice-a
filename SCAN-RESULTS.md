# Résultats de Scan - devsecops-exercice-a

**Date** : 26 janvier 2026
**Projet** : Node.js avec dépendances vulnérables

---

## Package.json

```json
{
  "dependencies": {
    "lodash": "4.17.20",
    "axios": "0.21.0",
    "minimist": "1.2.5",
    "node-fetch": "2.6.0"
  }
}
```

---

## Trivy (SCA - NVD)

```bash
trivy fs --scanners vuln .
```

### Résultat : 5 CVEs

| Library | CVE | Severity | Fixed Version |
|---------|-----|----------|---------------|
| lodash | CVE-2021-23337 | CRITICAL | 4.17.21 |
| axios | CVE-2021-3749 | HIGH | 0.21.2 |
| axios | CVE-2025-27152 | HIGH | 1.8.2 |
| minimist | CVE-2021-44906 | CRITICAL | 1.2.6 |
| node-fetch | CVE-2022-0235 | MEDIUM | 2.6.7 |

**Total** : 2 CRITICAL, 2 HIGH, 1 MEDIUM

---

## Snyk (SCA - Base propriétaire + Reachability)

### Résultat : 13 issues

| Library | Issues | Severity Max | Priority Score |
|---------|--------|--------------|----------------|
| axios@0.21.0 | 7 | HIGH | 696 |
| lodash@4.17.20 | 3 | HIGH | 681 |
| node-fetch@2.6.0 | 2 | MEDIUM | 539 |
| minimist@1.2.5 | 1 | LOW | 506 |

**Total** : 0 Critical, 3 High, 9 Medium, 1 Low

### Détails des vulnérabilités Snyk

#### axios@0.21.0 (7 issues)
- Server Side Request Forgery (SSRF)
- Regular Expression Denial of Service (ReDoS) - CVSS 7.5
- Cross-site Request Forgery (CSRF) - CVSS 7.1
- + 4 autres issues

#### lodash@4.17.20 (3 issues)
- Command Injection - CVSS 7.2
- Regular Expression Denial of Service (ReDoS) - CVSS 5.3
- Prototype Pollution - CVSS 6.9

#### node-fetch@2.6.0 (2 issues)
- Denial of Service (DoS) - CVSS 5.9
- Information Exposure - CVSS 6.5

#### minimist@1.2.5 (1 issue)
- Prototype Pollution - CVSS 3.7 (LOW)

---

## Comparaison Trivy vs Snyk

| Aspect | Trivy | Snyk |
|--------|-------|------|
| **Base de données** | NVD (publique) | Propriétaire + NVD |
| **Nombre d'issues** | 5 CVEs | 13 issues |
| **Analyse de reachability** | Non | Oui |
| **Score contextuel** | Non | Oui (Priority Score) |

### Cas remarquable : minimist

| Outil | Sévérité | Raison |
|-------|----------|--------|
| **Trivy** | CRITICAL | Score NVD brut (CVE-2021-44906 = 9.8) |
| **Snyk** | LOW (3.7) | Analyse contextuelle - vulnérabilité difficile à exploiter |

**Explication** : Snyk analyse si la vulnérabilité est réellement exploitable dans le contexte du projet :
- La fonction vulnérable est-elle appelée ?
- Les données utilisateur atteignent-elles cette fonction ?
- Dans ce cas, minimist n'est pas directement exposé aux entrées utilisateur

---

## Conclusion pour le TP

1. **Trivy** : Rapide, gratuit, basé sur NVD, peut générer des faux positifs de sévérité
2. **Snyk** : Plus précis grâce à l'analyse contextuelle, mais nécessite un compte
3. **Complémentarité** : Utiliser Trivy en CI/CD pour bloquer, Snyk pour prioriser

### Commandes de référence

```bash
# Trivy
trivy fs --scanners vuln .
trivy fs --scanners vuln --severity HIGH,CRITICAL .

# Snyk CLI
snyk auth
snyk test
snyk monitor
```

---

*Testé le 26 janvier 2026*
