+++
title = 'Gohugo Commands'
date = 2024-08-18T21:34:17-04:00
description = ""
tags = ["go", "hugo", "docs"]
+++

### Basic Hugo Commands

1. **Create a New Site:**
   ```bash
   hugo new site <site-name>
   ```
   This initializes a new Hugo site in a directory named `<site-name>`.

2. **Create a New Content File:**
   ```bash
   hugo new <section>/filename.md
   ```
   Creates a new content file in the specified section. For example, `hugo new posts/my-first-post.md` creates a new blog post in the `posts` section.

3. **Start the Development Server:**
   ```bash
   hugo server
   ```
   Runs the Hugo development server, which watches your files and rebuilds the site automatically when changes are detected. Access the site at `http://localhost:1313`.

4. **Build the Site:**
   ```bash
   hugo
   ```
   Generates the static files for your site in the `public/` directory. By default, this is a production build.

5. **Build with Drafts and Future Posts:**
   ```bash
   hugo -D -F
   ```
   Builds the site including draft posts (`-D`) and future-dated posts (`-F`).

6. **Preview the Site as Production:**
   ```bash
   hugo server --environment production
   ```
   Starts the development server but simulates a production environment.

### Advanced Hugo Commands

7. **Create a New Theme:**
   ```bash
   hugo new theme <theme-name>
   ```
   Creates a new Hugo theme in the `themes/` directory with the specified name.

8. **List Available Commands:**
   ```bash
   hugo help
   ```
   Displays a list of available Hugo commands and options.

9. **Show Version Information:**
   ```bash
   hugo version
   ```
   Displays the currently installed Hugo version.

10. **Clean Up Cache:**
    ```bash
    hugo mod clean
    ```
    Removes the Hugo modules cache.

11. **Install Dependencies:**
    ```bash
    hugo mod get
    ```
    Installs the Go modules dependencies for your site, useful when using Hugo themes or components as modules.

12. **Generate Only Specific Content:**
    ```bash
    hugo --contentDir="content/posts"
    ```
    Builds the site using content only from the specified directory.

13. **Build with Minified Assets:**
    ```bash
    hugo --minify
    ```
    Minifies the CSS, JavaScript, and HTML files in the generated output.

14. **Build a Site with a Different Configuration File:**
    ```bash
    hugo --config <config-file>
    ```
    Specifies a custom configuration file instead of the default `config.toml`.

15. **Deploy the Site:**
    ```bash
    hugo deploy
    ```
    Deploys your site to the configured deployment destination, such as Amazon S3, FTP server, etc.

### Common Flags

- **`-D`**: Include content marked as a draft.
- **`-F`**: Include content with a future publish date.
- **`-v`**: Enable verbose output, helpful for debugging.
- **`--minify`**: Minifies the output for better performance.
