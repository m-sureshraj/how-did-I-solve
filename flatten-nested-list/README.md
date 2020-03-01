## Flatten nested list
For this topic, I choose the nested directory structure. The implementation will be
similar to other nested data structures.

## Challenge
Write a function to print all files path in a directory and nested subdirectories.
The top-level files path should come before the subdirectories files path.

```
├── a.txt
├── dir_1
│   ├── b.txt
│   ├── dir_1.1
│   │   └── c.txt
│   └── e.txt
└── e.txt

> for the above directory structure, the output should be:

a.txt
e.txt
dir_1 → b.txt
dir_1 → e.txt
dir_1 → dir_1.1 → c.txt
```

### Data

```javascript
const files = [
  { type: 'file', name: 'a.txt' },
  {
    type: 'folder',
    name: 'dir_1',
    children: [
      { type: 'file', name: 'b.txt' },
      {
        type: 'folder',
        name: 'dir_1.1',
        children: [{ type: 'file', name: 'c.txt' }]
      },
      { type: 'file', name: 'e.txt' }
    ]
  },
  { type: 'file', name: 'e.txt' }
];
```

### Solution 1

```javascript
function printFilesPath(files) {
  function updateFileName(file) {
    return {
      ...file,
      name: `${this.parentName} → ${file.name}`
    };
  }

  const processedFiles = [];
  let file;

  while (files.length) {
    file = files.shift();

    if (file.type === 'folder') {
      files.push(
        ...file.children.map(updateFileName, {
          parentName: file.name
        })
      );
    } else {
      processedFiles.push({ path: file.name });
    }
  }

  return processedFiles;
}

printFilesPath(files).map(file => file.path).join('\n');
```

### Output

```
a.txt
e.txt
dir_1 → b.txt
dir_1 → e.txt
dir_1 → dir_1.1 → c.txt 
```

### Solution 2
> Without considering files path order

```javascript
function printFilesPath(
  files,
  processedFiles = [],
  prevPath = ''
) {
  let path = '';

  files.forEach(file => {
    path = prevPath ? `${prevPath} → ${file.name}` : file.name;

    if (file.type === 'folder') {
      return printFilesPath(
        file.children,
        processedFiles,
        path
      );
    }

    processedFiles.push({ path });
  });

  return processedFiles;
}

printFilesPath(files).map(file => file.path).join('\n');
```

OR, Functional approach. Contributed by [Dasith](https://github.com/DasithKuruppu)

```javascript
const printFilesPath = (nestedFiles, rootDirectory = '') =>
  nestedFiles.reduce(
    (prev, { type, name, children }) => [
      ...prev,
      ...(type === 'folder'
        ? printFilesPath(children, `${rootDirectory}${name} → `)
        : [rootDirectory + name])
    ],
    []
  );

printFilesPath(files).map(file => file.path).join('\n');
```

### Output

```
a.txt
dir_1 → b.txt
dir_1 → dir_1.1 → c.txt
dir_1 → e.txt
e.txt
```