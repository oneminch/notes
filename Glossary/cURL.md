- Stands for "Client URL"
- A command-line tool used for transferring data and making requests across various protocols, HTTP, HTTPS, FTP, and more.
- Widely used for testing APIs, downloading files, and automating tasks in scripts.

- By default, curl uses the GET method, which is suitable for fetching data.

## Usage

- Make a GET request to a URL:

```bash
# Retrieves the HTML content of the URL and displays it in the terminal.
curl http://example.com
```

- Save the output of a curl command to a file, use the `-o` option:

```bash
# Saves the HTML content to a file named `output.html`.
curl -o output.html http://example.com
```

- Make a POST request using `-d`:

```bash
curl -d "name=John&phone=123456789" http://example.com/api
```

- Include custom headers in a request using `-H`:

    ```bash
    curl -H "Authorization: Bearer token" http://example.com/api
    ```

- Specify a custom HTTP method (`-X`, `--request`)

```bash
curl -X DELETE https://example.com/resource
```

- Suppress progress output using silent mode (`-s`):

```bash
# Silent Mode
curl -s http://example.com
```
