
```sh
go build -tag netgo -installsuffix netgo --ldflags '-exldflags "-static"' -o <binary>
```

Linuxなどの環境のglibc のversionが古い場合、上記でglibcを含めることができる
ただし、ライセンスの問題があるため、注意が必要