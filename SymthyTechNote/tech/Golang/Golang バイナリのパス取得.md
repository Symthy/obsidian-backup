
```go
func GetHomeDirectory() (string, error) {
  thisPath, err := os.Executable()
  if err != nil {
    return "", err
  }
  // バイナリが置いてあるディレクトリのパス取得
  return filepath.Join(filepath.Dir(thisPath), "..")
}
```