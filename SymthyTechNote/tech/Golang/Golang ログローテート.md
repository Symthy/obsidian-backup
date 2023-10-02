
lumberjack

```go

import (
    "io"
    "log"

    "gopkg.in/natefinch/lumberjack.v2"
)

func BuildLumberjackLogger(logFilePath string, maxSizeInMB, maxBackupNum int) io.Writer {
    return &lumberjack.Logger{
        Filename:   logFilePath,
        MaxSize:    maxSizeInMB,
        MaxBackups: maxBackupNum,
        Compress:   false,
    }
}

func InitLogger(logWriter io.Writer) {
    log.SetOutput(logWriter)
}
```