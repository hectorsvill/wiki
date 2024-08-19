+++
title = 'Building a Directory Tree Generator in Go'
date = 2024-08-18T21:04:21-04:00
description = ""
tags = ["go"]
+++

When working with complex file structures, visualizing the layout of directories and files can be incredibly helpful. Whether you're onboarding new developers, documenting a project, or simply trying to get a better grasp of a large codebase, having a clear directory tree can save time and prevent confusion. In this blog post, we’ll explore how to build a directory tree generator in Go that can output the structure in both text and JSON formats.

## Overview of the Directory Tree Generator

This Go program generates a directory tree starting from a specified directory and outputs it in either a text or JSON format. The program is controlled through command-line flags, allowing you to specify the starting directory, output format, and whether to print the tree to the console.

### Command-Line Options

The program accepts the following command-line options:
- `-d`: Specifies the starting directory for the directory tree. The default is the current directory (`.`).
- `-f`: Specifies the output format. You can choose between `txt` (text format) and `json` (JSON format). The default is `txt`.
- `-p`: If set, the directory tree is printed to the console instead of being written to a file.

## Breaking Down the Code

Let's dive into the code to understand how the program works.

### 1. Flag Initialization

```go
var (
	startDir string
	format   string
	print    bool
)

func init() {
	flag.StringVar(&startDir, "d", ".", "starting directory")
	flag.StringVar(&format, "f", "txt", "output format (txt or json)")
	flag.BoolVar(&print, "p", false, "print to console")
	flag.Usage = func() {
		fmt.Fprintf(os.Stderr, "Usage: %s [options]\n\nOptions:\n", os.Args[0])
		flag.PrintDefaults()
	}
	flag.Parse()
}
```

Here, we define three variables: `startDir`, `format`, and `print`, which are populated using the `flag` package. These flags allow the user to customize the program's behavior from the command line.

### 2. Directory Tree Structure

```go
type DirEntry struct {
	Name    string      `json:"name"`
	IsDir   bool        `json:"is_dir"`
	Entries []*DirEntry `json:"entries,omitempty"`
}
```

The `DirEntry` struct represents a single file or directory. The `Entries` field is a slice of pointers to other `DirEntry` objects, allowing the struct to represent a tree structure recursively. The JSON tags are used to control the JSON output format.

### 3. Main Function

```go
func main() {
	if print || (format == "" && startDir == "." && !print) {
		walkDir(startDir, 0, "", os.Stdout)
	} else if format == "json" {
		root := &DirEntry{Name: startDir, IsDir: true}
		walkDirJSON(startDir, root)

		f, _ := os.Create("dir_tree.json")
		defer f.Close()
		enc := json.NewEncoder(f)
		enc.SetIndent("", "  ")
		enc.Encode(root)
	} else {
		f, _ := os.Create("dir_tree.txt")
		defer f.Close()
		walkDir(startDir, 0, "", f)
	}
}
```

The `main` function is the entry point of the program. Depending on the flags, it decides whether to print the directory tree to the console or write it to a file in either text or JSON format.

### 4. Walking the Directory Tree

The directory tree is generated using two different functions: `walkDir` for text output and `walkDirJSON` for JSON output.

```go
func walkDirJSON(path string, parent *DirEntry) {
	files, _ := ioutil.ReadDir(path)
	for _, file := range files {
		entry := &DirEntry{Name: file.Name(), IsDir: file.IsDir()}
		parent.Entries = append(parent.Entries, entry)
		if file.IsDir() {
			walkDirJSON(filepath.Join(path, file.Name()), entry)
		}
	}
}
```

The `walkDirJSON` function recursively traverses the directory structure, creating `DirEntry` objects for each file and directory, and appending them to their parent directory’s `Entries` slice.

```go
func walkDir(path string, level int, prefix string, w io.Writer) {
	files, _ := ioutil.ReadDir(path)
	for i, file := range files {
		if i == len(files)-1 {
			fmt.Fprintf(w, "%s└── %s\n", prefix, file.Name())
			if file.IsDir() {
				walkDir(filepath.Join(path, file.Name()), level+1, prefix+"    ", w)
			}
		} else {
			fmt.Fprintf(w, "%s├── %s\n", prefix, file.Name())
			if file.IsDir() {
				walkDir(filepath.Join(path, file.Name()), level+1, prefix+"│   ", w)
			}
		}
	}
}
```

The `walkDir` function similarly traverses the directory structure, but instead of building a JSON object, it writes a formatted text representation of the tree to the specified `io.Writer`.

### 5. Generating the Output

The program writes the directory tree to a file or prints it to the console, depending on the flags set by the user. For text output, it uses a simple, visually clear tree format. For JSON output, it creates a structured representation of the directory that can be easily parsed by other tools.

## Conclusion

This Go program provides a flexible and efficient way to generate a directory tree. By supporting both text and JSON output formats, it caters to a wide range of use cases, from simple visualization to integration with other software. Whether you're documenting your codebase or just trying to get a handle on a sprawling project, this directory tree generator is a valuable tool to have in your toolkit.

github Repository: [Directory-Tree-Generator](https://github.com/hectorsvill/Directory-Tree-Generator/tree/main)

