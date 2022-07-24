# go语言文件相关的处理
- [go语言文件相关的处理](#go语言文件相关的处理)
  - [filepath.Walk](#filepathwalk)
  - [filepath.WalkDir](#filepathwalkdir)
  - [tar包解压](#tar包解压)

## filepath.Walk
[文件递归速度比较](https://blog.csdn.net/shan0304/article/details/97685414)

```
//GetAllFileIncludeSubFolder 递归获取某个目录下的所有文件
func GetAllFileIncludeSubFolder(folder string) ([]string, error) {
    var result []string

    filepath.Walk(folder, func(path string, fi os.FileInfo, err error) error {
        if err != nil {
            log.Println(err.Error())
            return err
        }

        if !fi.IsDir() {
            //如果想要忽略这个目录，请返回filepath.SkipDir，即：
            //return filepath.SkipDir
            result = append(result, path)
        }

        return nil
    })

    return result, nil
}
```

## filepath.WalkDir

```
func GetAllFileIncludeSubFolder(folder string) ([]string, error) {
	var targets []string
	maxDepth := strings.Count(folder, string(os.PathSeparator))
	_ = filepath.WalkDir(folder, func(fPath string, d fs.DirEntry, err error) error {
		if err != nil {
			return err
		}
		if d.IsDir() && strings.Count(fPath, string(os.PathSeparator)) > maxDepth {
			return fs.SkipDir
		}
		if !d.IsDir() {
			targets = append(targets, filepath.Base(fPath))
		}
		return nil
	})
	return targets, nil
}
```

## tar包解压

```
func deCompress(tarFile, dest string) error {
	srcFile, err := os.Open(tarFile)
	if err != nil {
		return err
	}
	defer srcFile.Close()
	gr, err := gzip.NewReader(srcFile)
	if err != nil {
		return err
	}
	defer gr.Close()
	tr := tar.NewReader(gr)
	for {
		hdr, err := tr.Next()
		if err != nil {
			if err == io.EOF {
				break
			} else {
				return err
			}
		}
		filename := filepath.Join(dest, hdr.Name)
		_ = os.MkdirAll(filepath.Dir(filename), 0755)
		if hdr.Size == 0 {
			_ = os.MkdirAll(filename, 0755)
			continue
		}
		file, err := os.Create(filename)
		if err != nil {
			return err
		}
		_, _ = io.Copy(file, tr)
		_ = os.Chmod(filename, 0777)
	}
	return nil
}
```