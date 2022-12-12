# 概要
DDDサンプルコード

# Goバージョン
Go `1.19`

# API起動方法
1. DB起動(`ddd-code-example/setup`ディレクトリ上で実行)
```
docker-compose up -d
```

2. 依存packageダウンロード(`ddd-code-example/src`ディレクトリ上で実行)
```
make setup
```

3. API起動(`ddd-code-example/src`ディレクトリ上で実行)
```
make run
```

# 実装例
- repositoryパターン
  - ファイル登録API
  - スタンプ登録API
- query serviceパターン
  - スタンプ一覧取得API
- ページング
  - スタンプ一覧取得API
- S3 client
  - ファイル登録API
- CloudFront client
  - スタンプ一覧取得API

# Request, Response例
<details>
<summary>ファイル登録API</summary>

リクエスト
```
POST http://localhost:1323/ddd/files

{
    "files": [
        {
            "ext": "png",
            "size": 202400
        },
        {
            "ext": "png",
            "size": 302400
        }
    ]
}
```

レスポンス
```
200 OK

{
    "files": [
        {
            "id": 1,
            "mime": "image/png",
            "path": "357c048357a2f82973875e7c8cbea9dc/eafb8d37-32c9-4aaf-95ec-e3737113f42e",
            "presignedUrl": "https://apotool-interview-dev.s3.ap-northeast-1.amazonaws.com/357c048357a2f82973875e7c8cbea9dc/eafb8d37-32c9-4aaf-95ec-e3737113f42e?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIA2QEXIXPFNQWPJBNO%2F20221130%2Fap-northeast-1%2Fs3%2Faws4_request&X-Amz-Date=20221130T123422Z&X-Amz-Expires=300&X-Amz-SignedHeaders=host&X-Amz-Signature=06ff3393f5c8ffb57f75f42b0730889a726735075ac4ea65154435461ab182ba"
        },
        {
            "id": 2,
            "mime": "image/png",
            "path": "357c048357a2f82973875e7c8cbea9dc/6eb23803-8c67-4272-9fa3-10b37d16334f",
            "presignedUrl": "https://apotool-interview-dev.s3.ap-northeast-1.amazonaws.com/357c048357a2f82973875e7c8cbea9dc/6eb23803-8c67-4272-9fa3-10b37d16334f?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIA2QEXIXPFNQWPJBNO%2F20221130%2Fap-northeast-1%2Fs3%2Faws4_request&X-Amz-Date=20221130T123422Z&X-Amz-Expires=300&X-Amz-SignedHeaders=host&X-Amz-Signature=32dc1cfd02bef57da75f822d86f76931a4f7e1a93371c177cb942ea65c4e1f32"
        }
    ]
}
```
</details>

<details>
<summary>スタンプ登録API</summary>

リクエスト
```
POST http://localhost:1323/ddd/stamps

{
    "stamp": {
        "name": "スタンプ1",
        "originFileID": 1,
        "thumbnailFileID": 2
    }
}
```

レスポンス
```
200 OK

{
    "stamp": {
        "id": 1
    }
}
```
</details>

<details>
<summary>スタンプ一覧取得API</summary>

リクエスト
```
GET http://localhost:1323/ddd/stamps?limit=5&offset=0
```

レスポンス
```
200 OK

{
    "totalCount": 6,
    "prevOffset": null,
    "nextOffset": 5,
    "stamps": [
        {
            "id": 6,
            "name": "スタンプ6",
            "createdAt": "2022-11-30T03:00:00Z",
            "originPresignedURL": "https://media.interview-backend.stransa.com/357c048357a2f82973875e7c8cbea9dc/f1af8ccc-db7a-4cfc-bb3a-4f8b9cda3e61?Expires=1669834800&Signature=ynfmQ~WPoQ6XInEiS-Buycg4KiKnCqeWI4h~~NRAxFacqqrE7If2sV94YDWan9wET4TGVfDVxEl0XCTxoRIXqjrMWtP3UoE-oWnrOFYpl3BLYT6TIuBxvtXZRH~NU440wnTe6XXjkeImoFNJo5UjPkF2~tWc1fMEM8vp9~Qlke22dsXTDW2Wi0HMPm673aa~JdMnPKL9OVpUariUeFZVaMJnWTypE78Wtr4tNOb6M2RJsLgnhu0OtaXevUI1o21Wynv0OnyI~29gTjsWVaIXqpfNm5H3DAALe1yYvzB-mbuDkvrwJEERL1K12Q-7DU7-Me5dJQ2~tDdA3CLVTTJU4g__&Key-Pair-Id=K1PVL39I1P1DCI",
            "thumbnailPresignedURL": "https://media.interview-backend.stransa.com/357c048357a2f82973875e7c8cbea9dc/f1af8ccc-db7a-4cfc-bb3a-4f8b9cda3e62?Expires=1669834800&Signature=K0QfPPWw4m6ABbRCRIzVcWF113UZu03SkfyUb3cZP-wfq8DXTWjLw72evL5ptVYk5InTUawYERHvXRgN1V0rP93G8rI4xd4zLtInfkUtkJiSw0bjSNXSgt3oH9jWGselNOvKCEJ8FD1jrJm3h6g-hRMUlYkmrHTkWHPx3SlEgMlfZLI-z6bjwVjufjWz-ktfawj3DK6VcWi4dqULidsnzjSeuYoIsm8ZkNBEeBtOfVMhz-R3TbVGWPJqdSpzdTZtIR21Jz-VGwqtf3AIgJpzw6Fw9b-KAooNdYLOecyVSrsC-B8mMNZ2MYVhDIYCNL3ZQXYqCO4RdarUyZ~hDHzEpQ__&Key-Pair-Id=K1PVL39I1P1DCI"
        },
        {
            "id": 5,
            "name": "スタンプ5",
            "createdAt": "2022-11-30T03:00:00Z",
            "originPresignedURL": "https://media.interview-backend.stransa.com/357c048357a2f82973875e7c8cbea9dc/f1af8ccc-db7a-4cfc-bb3a-4f8b9cda3e59?Expires=1669834800&Signature=xQhYa-NaHe4iEP9W3Rry8YTe5GXtrCT78xtw5uBdsZBcT8rvON6OO61w0DoMNbyTU98Fd6N3-2FtivkdXovUxWkbkofags~U4c7L~KZRa~vEH2SK08TvVwSRdm-XhCNLSuTuWycFLXt7UPpyv19raCGsiAG1kqDk-HsH6CZqqsqk-Qe4BJiONuHNp6LRraCp9EhSF3hRQZjkFMpm8Zx8rOu5tiboD29xqd61RJ~fnZXe9KmzC-cNqcbB5SYJ7w2WR6DGEA3WUQ-Tzv~-6wPda688~fi91NDdhS0Tuz4AmoCCrz4-hwy~4vvG4Mq4FZGG4to5wT9UKn1RQT0Kjk25Ww__&Key-Pair-Id=K1PVL39I1P1DCI",
            "thumbnailPresignedURL": "https://media.interview-backend.stransa.com/357c048357a2f82973875e7c8cbea9dc/f1af8ccc-db7a-4cfc-bb3a-4f8b9cda3e60?Expires=1669834800&Signature=hPqBXV~w4~Bk5E8Qi3UgtoGZ5UZb5aQtOh9mD53TpZ7EPxBxHRPFyGaAb26UadgTScU1b~nWU5bNBj~wJeb8zHwQe13LOYf0THc1zm8L6LaZa6Jw3hXH0DT-d5CbdxW9FgR72CkZCFjjZQk21jwet~i0mdffK8gZmFu~MmVCEmJv3qXkpEIhZX8tU5BN6q0JKTW72V~xXhxFqvUhKEHX3esbNPyCtjSkkznWJ--ePbrI87yYM-p0t-FpVHtg72zkVkfkSuWeE~9yX1wfBkXeJbZBEgxulw94m2mGXdsvxlvCZtR9b3250FLKiooc8rl8TJcxE8FZU00TU~Na4gtJZA__&Key-Pair-Id=K1PVL39I1P1DCI"
        },
        {
            "id": 4,
            "name": "スタンプ4",
            "createdAt": "2022-11-30T03:00:00Z",
            "originPresignedURL": "https://media.interview-backend.stransa.com/357c048357a2f82973875e7c8cbea9dc/f1af8ccc-db7a-4cfc-bb3a-4f8b9cda3e57?Expires=1669834800&Signature=wiQgz8HJy~KgipmkP-UmS~XOEmXcLyTOO1GMjynDLjStMlxX04P~mXT8c~G4gx8oRZaVT4tvKTPDSkMnR1E7H6hvEgO22gHcBZoXKtdXpHw-Y6fVIDK2TewgUDHSgqUS8dULd6e6b0jHXNGz6kTInVjlCEuEc49Ook1uwEDGNmjydTJl-0O05938IXdsIU1V1LupYAM8uV-ltz16s04J8GmiXnL6Xz4YXg5550y3~OfAws~42b1kgdenIzeJaO4oyyyA0BdVu5uCFBdjtr1dFoxsZN5D1xgC37~g8nq2tZO7N4qL0Y9lveFsAwoPqovnteFMRO5Qdkejn~TgWCKgDQ__&Key-Pair-Id=K1PVL39I1P1DCI",
            "thumbnailPresignedURL": "https://media.interview-backend.stransa.com/357c048357a2f82973875e7c8cbea9dc/f1af8ccc-db7a-4cfc-bb3a-4f8b9cda3e58?Expires=1669834800&Signature=SBNNmiXRutixEfQsrcL7yEOg9Cr1okAPMHsRFhkF08KCWx1cDhOBTID8TAoSBYcBODXZelW6ynkcBn7qyuCu-agxRK5l0I8K2ulhnSEsZzqutNk6bgUWZGz3yHa3oe0Gol-f4g0R-pGC21FuryBrbz44xelJVwK6x9en9HIcpmQMDNy0WN2JJx7W1PIBWBEnrJ5ES6rwXMGK7aFjl-VPhY7ySW3MMvEGEkT-QsKPA-WP-lqkXhJUS4GQB0bf7s3qHJxpY8~dTEiiKRQITGM6QJm1Wvp7Cj9XkNhpYkxIXdEgpOVEeZZlHm70ZhJzxJ9OzDYGge0V4cDj9MQ-bcNy-A__&Key-Pair-Id=K1PVL39I1P1DCI"
        },
        {
            "id": 3,
            "name": "スタンプ3",
            "createdAt": "2022-11-30T03:00:00Z",
            "originPresignedURL": "https://media.interview-backend.stransa.com/357c048357a2f82973875e7c8cbea9dc/f1af8ccc-db7a-4cfc-bb3a-4f8b9cda3e55?Expires=1669834800&Signature=lgSDheyst90eq3-Md7UjTfEERNJNojHvVdvt7YyOQVrB70qolJYr04zmW~UEM8AIfRqiYiBfmRgby6W3UwnyiujyY9DJxTUjFPjf9HlHTBTCdmIOtPam0rCFR7xaMECMifxVpnpy9jz0wRf81g3lzikRcYdL6-o1Z95IMsegloLRdXjIAND-bcCBgrNriKCNbKwM2Rqrf6hNNU3pF9GqAidoPivPyjqUGAByvlJF2Iqx1DK86V~1cjusEIMblJDxdlcILMWAPmjaHtjy47O0wIn62cHIsmeE6uj5aDXJ4gNfW7K5FQynhN2x1ZhpUwCm-wPwuteYWBq1wJTKUwCkZw__&Key-Pair-Id=K1PVL39I1P1DCI",
            "thumbnailPresignedURL": "https://media.interview-backend.stransa.com/357c048357a2f82973875e7c8cbea9dc/f1af8ccc-db7a-4cfc-bb3a-4f8b9cda3e56?Expires=1669834800&Signature=VE2UqUhku6Q-IWBt6~c-~OqZAj~aDX4JKKrijnsbsyPO6A9pkWT-t95Lij1IZPthUbUN8Zb~E8OmAhLjqZ8Mp1Kee5Rs-zG2wi6eQSitMPRYr5vaUQvVbbfTe66K8ybZdv0Q1PhOHB0o1QAs4~iSPgJi10HOCRhrk4ablMIDKDbHKhocPiQgtJmjjyJ44EECkIBZsWqf2PSH9hVZR7BXJsrvtxx~56WchgEyOGS1ILfLOWmldx9mBgw4DHl4kRjxfUbdndgOoWR~ivP7OQTENapVl6aguvicZFXlXaO~tGvq8RrEeVVwr0FCXjq42GLUtwIWMaYZ-6vVyJ9LGVS8sQ__&Key-Pair-Id=K1PVL39I1P1DCI"
        },
        {
            "id": 2,
            "name": "スタンプ2",
            "createdAt": "2022-11-30T03:00:00Z",
            "originPresignedURL": "https://media.interview-backend.stransa.com/357c048357a2f82973875e7c8cbea9dc/f1af8ccc-db7a-4cfc-bb3a-4f8b9cda3e53?Expires=1669834800&Signature=Zx9X5n3o~bSlqhhNiX8nZxeruJgdzdIW1FbWibnI~jkPrLmW9sVAaZ5s8ulydwTJ1uoizh33X78TWmO-kd3U52rYGJpf9O7Kw41iOYKAe~xz7I0P-RtOZu3J-zlLUuf4maviKNexuA8fy0jTqMs3OhVEath1aTVSHPVt1cKnHjk2q7XlfJbFlQDvXmQ3Nig4jslM8oP~xohUZaswajzonk4M0Cx6W5V8GTfwJX-uOz8ShcELLcecU85apkbMspZG8lDapKC~4IM1aji0xX-RtS50bl7Tv3KeBKeoZic4mlAX8Bnq5dMAKQoKuIGUSKufsiFaneDocXkzQCfG-0pvZQ__&Key-Pair-Id=K1PVL39I1P1DCI",
            "thumbnailPresignedURL": "https://media.interview-backend.stransa.com/357c048357a2f82973875e7c8cbea9dc/f1af8ccc-db7a-4cfc-bb3a-4f8b9cda3e54?Expires=1669834800&Signature=cXMYTZOIAlq3RzbHnh8xl0SlMbYNIKRmd9mNcDF-nApK4F7h51XiRq2mzBQwhHLfsY0t3cSY5R0l6UiCFvkVPXPY5pR0lmpyJ48ek8Wnn2IBLI72TXDve-J5EvaBblBoptcDWZoiN63PRS~tYnqa2Un0lQ3dx3wOx9UvVx5ExklFnf1FlFqtv-xCuFiEAUiXTF4KbddUFdhDqkW8EazbABZDjNhGz7qE-8TsaxcF8TZznCiwikJeVU7WOZ8FbwrKYwr-4zcL~E-N3jnX10orxmwo12aA9OrrqqVxDNPlJCxm1PZKxn2yihbYdTikV-nCO~tXDZAo57dDADE6kSrajQ__&Key-Pair-Id=K1PVL39I1P1DCI"
        }
    ]
}
```
</details>
