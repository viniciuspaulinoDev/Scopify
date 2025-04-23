# Scopify - Netify.ai Reconnaissance Tool

<!-- Placeholder for Badges -->
[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
<!-- Add other badges here (Python version, etc.) -->

**Scopify** is a Python command-line tool designed for penetration testers and bug bounty hunters to quickly gather and analyze infrastructure information (CDN, Hosting, SaaS) for a target company by scraping `netify.ai`. Developed by [@jhaddix](https://x.com/Jhaddix) and [Arcanum Information Security](https://www.arcanum-sec.com/).

It optionally leverages OpenAI's API to provide AI-driven analysis of the gathered infrastructure, highlighting potential areas of interest and suggesting reconnaissance methodologies.

## Setup

1.  **Clone the repository (if applicable) or ensure you have the files:**
    *   `scopify.py`
    *   `requirements.txt`

2.  **Create a Python virtual environment:**
    ```bash
    python3 -m venv venv
    ```

3.  **Activate the virtual environment:**
    *   On Linux/macOS:
        ```bash
        source venv/bin/activate
        ```
    *   On Windows:
        ```bash
        .\venv\Scripts\activate
        ```

4.  **Install dependencies:**
    ```bash
    pip install -r requirements.txt
    ```

## Usage

Run the script from the command line using the `-c` or `--company` flag followed by the company name (lowercase, use hyphens if needed based on `netify.ai`'s URL structure).

```bash
python scopify.py -c <company-name> [--analyze]
```

**Arguments:**

*   `-c`, `--company`: (Required) The target company name.
*   `--analyze`: (Optional) Enables AI analysis of the scraped data using OpenAI. Requires the `OPENAI_API_KEY` environment variable to be set.

**Environment Variable for AI Analysis:**

To use the `--analyze` feature, you **must** set your OpenAI API key as an environment variable named `OPENAI_API_KEY` before running the script.

*   On Linux/macOS:
    ```bash
    export OPENAI_API_KEY='your-api-key-here'
    ```
*   On Windows (Command Prompt):
    ```bash
    set OPENAI_API_KEY=your-api-key-here
    ```
*   On Windows (PowerShell):
    ```bash
    $env:OPENAI_API_KEY = 'your-api-key-here'
    ```
Replace `'your-api-key-here'` with your actual OpenAI API key.

**Example (Basic):**

```bash
python scopify.py -c walmart
```

```bash
--- CDNs ---
CDNs                           # of IPs
---------------------------------------------
Akamai                         10382
Amazon CloudFront              3076
Fastly                         331
Cloudflare                     16
Azure Front Door               5


--- Hosting ---
Cloud Hosts                    # of IPs
---------------------------------------------
Google Hosted                  245
Amazon AWS                     28
Unitas Global                  17
Microsoft Azure                15
Equinix                        3
Cloudinary                     1
Google Cloud Platform          1
Rackspace                      1
WP Engine                      1


--- SaaS ---
SaaS                           # of IPs
---------------------------------------------
Email Studio                   221
Adobe EM                       168
Adobe Ads                      59
SendGrid                       16
Salesforce                     6
Validity                       5
LexisNexis Risk                3
Mailgun                        2
Pendo                          2
MarkMonitor                    1
Medallia                       1
```

**Example (With AI Analysis):**

```bash
--- AI Analysis --- 
Analyzing infrastructure data with OpenAI...

1. CDN OBSERVATIONS

  - Akamai (10 382 IPs)  
    • Global edge network with robust WAF capabilities (Kona, GTM, Bot Manager).  
    • Look for subdomain–origin mismatches (staging/test instances) via wildcard DNS or certificate transparency logs.  
    • Test Host header and path‐based routing bypasses to reach internal origins.

  - Amazon CloudFront (3 076 IPs)  
    • Common misconfiguration: S3 bucket origin left public or locked behind custom domain.  
    • Probe for Host header overrides and unvalidated redirects.  
    • Enumerate unused edge configurations via custom CNAMEs in DNS records.

  - Fastly (331 IPs)  
    • Default VCL snippets can leak backend hostnames.  
    • Potential open proxy behavior if VCL not locked down.  
    • Fingerprint backend exposures by sending unusual HTTP verbs and headers.

  - Cloudflare (16 IPs)  
    • WAF and rate‐limiting active, but origin IPs often exposed via DNS history or archived scan data.  
    • Check for subdomain takeover on unclaimed DNS entries (e.g., *.walmart.com pointing to Cloudflare but unregistered in Cloudflare dashboard).

  - Azure Front Door (5 IPs)  
    • Host rewrite misconfigurations may allow Host header bypass.  
    • Verify custom domain validation to prevent unwanted CNAME mapping.



2. HOSTING OBSERVATIONS

  - Google Hosted (245 IPs)  
    • High volume suggests static asset or microservice hosting.  
    • GCP metadata service attacks if misconfigured; check for exposed metadata endpoints via SSRF.

  - Amazon AWS (28 IPs)  
    • Potential IAM role exposure in EC2 metadata; test for SSRF.  
    • Publicly exposed services (e.g., ELB, API Gateway) could reveal unused endpoints.

  - Unitas Global (17 IPs) & Equinix (3 IPs)  
    • Likely colocation/shared transit.  
    • Scan for open management interfaces (SSH, RDP) and default credentials.

  - Microsoft Azure (15 IPs)  
    • Similar SSRF/metadata considerations.  
    • Check for Azure‑specific services (App Service, Function Apps) with default subdomains.

  - Single‐IP Hosts (Cloudinary, GCP, Rackspace, WP Engine)  
    • Specialized services; enumerate hostnames to uncover asset footprint or CMS backends.



3. SAAS OBSERVATIONS

  - Email Studio (221 IPs), Adobe EM (168 IPs), Adobe Ads (59 IPs)  
    • Marketing automation platforms.  
    • Inspect tracking pixels, CORS policies, and parameter injection in campaign URLs.

  - SendGrid (16 IPs), Mailgun (2 IPs)  
    • Email delivery APIs.  
    • Test URL callbacks, webhook endpoints, and API key exposure in front‑end code.

  - Salesforce (6 IPs)  
    • CRM integration points; possible OAuth endpoints.  
    • Look for custom subdomains (e.g., mycompany.salesforce.com) ripe for subdomain takeover or exposed metadata.

  - Validity (5 IPs), LexisNexis Risk (3 IPs)  
    • Data quality/risk scoring.  
    • Assess JavaScript SDK integrations for unsafe POST requests or leakage of PII.

  - Pendo (2 IPs), Medallia (1 IP), MarkMonitor (1 IP)  
    • In‑app analytics and feedback widgets.  
    • Scrutinize embedded scripts for client‑side logic flaws (XSS, insecure storage).



4. METHODOLOGY

  - Subdomain Enumeration  
    • Use modern tooling to exhaust DNS, certificate transparency, and brute lists.

  - WAF Fingerprinting & Bypass Testing  
    • Send crafted payloads and monitor differences in response codes/headers to distinguish between CDNs.

  - Origin Exposure Testing  
    • Override DNS resolution locally to connect directly to edge or origin IPs and bypass CDN protections.

  - Cloud Storage Enumeration  
    • Query GrayHatWarfare for public bucket listings:  
      https://buckets.grayhatwarfare.com/files?keywords=walmart

  - SaaS Integration Review  
    • Crawl front‑end code for third‑party SDKs, inspect endpoints, test CORS and authentication flows.

  - Metadata & SSRF Checks  
    • Target AWS/Azure/GCP metadata URLs via any SSRF‑susceptible parameter.

  - Service Scan & Port Verification  
    • Validate open ports and banner grabs on hosting IP ranges to identify exposed management interfaces.

All findings should guide focused audit scopes and safe‑safe proof‑of‑concepts in line with Walmart’s bug bounty policy.
```

**Output:**

The script will output sorted tables for CDNs, Hosting providers, and SaaS platforms used by the specified company. If `--analyze` is used and the API key is set correctly, an AI-generated summary and analysis relevant to penetration testing/bug bounty hunting will be printed after the tables.

If the company page is not found (404 error), an error message will be displayed suggesting you check the company name spelling and format.

A `debug_<company-name>.html` file is also created/overwritten in the same directory, containing the raw HTML source of the scraped page (or the error page) for debugging purposes.

## License

This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.

## Contributing

Contributions are welcome! Please feel free to submit pull requests or open issues to suggest features or report bugs.

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## Disclaimer

This tool is intended for educational and authorized security testing purposes only. Use it responsibly and ethically. The developers assume no liability and are not responsible for any misuse or damage caused by this tool. Always ensure you have explicit permission before scanning any target.

## Acknowledgements

*   Data sourced from [Netify.ai](https://www.netify.ai/)
*   AI Analysis powered by [OpenAI](https://openai.com/)
