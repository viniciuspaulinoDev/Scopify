# Scopify - Netify.ai Reconnaissance Tool

<!-- Placeholder for Badges -->
[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
<!-- Add other badges here (Python version, etc.) -->

**Scopify** is a Python command-line tool designed for penetration testers and bug bounty hunters to quickly gather and analyze infrastructure information (CDN, Hosting, SaaS) for a target company by scraping `netify.ai`.

It optionally leverages OpenAI's API to provide AI-driven analysis of the gathered infrastructure, highlighting potential areas of interest and suggesting reconnaissance methodologies (without naming specific tools).

<!-- Placeholder for Demo/Screenshot -->
## Demo

*(Add a GIF or screenshot here showing the tool in action)*

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

**Example (With AI Analysis):**

```bash
# First, set the environment variable (example for Linux/macOS)
export OPENAI_API_KEY='sk-...' 

# Then run the tool with the --analyze flag
python scopify.py -c walmart --analyze 
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
