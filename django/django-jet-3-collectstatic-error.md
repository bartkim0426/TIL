# django jet 3 collectstatic error

django jet 3를 사용중에 프로덕션/스테이징 환경에서 boto3 (s3)로 collectstatistic을 하면 몇몇 폰트가 깨지는 증상이 발생한다.

처음 한두번은 뭔가 꼬인거라고 생각하였으나 지속적으로 발생하여 뭔가 문제가 있다고 판단하여 해결책을 정리해본다.

```
Found another file with the destination path 'admin/css/widgets.css'. It will be ignored since only the first encountered file is collected. If this is not what you want, make sure every static file has a unique path.
Found another file with the destination path 'admin/css/login.css'. It will be ignored since only the first encountered file is collected. If this is not what you want, make sure every static file has a unique path.
Found another file with the destination path 'admin/css/dashboard.css'. It will be ignored since only the first encountered file is collected. If this is not what you want, make sure every static file has a unique path.
Found another file with the destination path 'admin/css/forms.css'. It will be ignored since only the first encountered file is collected. If this is not what you want, make sure every static file has a unique path.
Found another file with the destination path 'admin/css/fonts.css'. It will be ignored since only the first encountered file is collected. If this is not what you want, make sure every static file has a unique path.
Found another file with the destination path 'admin/css/rtl.css'. It will be ignored since only the first encountered file is collected. If this is not what you want, make sure every static file has a unique path.
Found another file with the destination path 'admin/css/base.css'. It will be ignored since only the first encountered file is collected. If this is not what you want, make sure every static file has a unique path.
Found another file with the destination path 'admin/css/changelists.css'. It will be ignored since only the first encountered file is collected. If this is not what you want, make sure every static file has a unique path.
Found another file with the destination path 'admin/js/SelectFilter2.js'. It will be ignored since only the first encountered file is collected. If this is not what you want, make sure every static file has a unique path.
Found another file with the destination path 'admin/js/admin/RelatedObjectLookups.js'. It will be ignored since only the first encountered file is collected. If this is not what you want, make sure every static file has a unique path.
Found another file with the destination path 'admin/js/admin/DateTimeShortcuts.js'. It will be ignored since only the first encountered file is collected. If this is not what you want, make sure every static file has a unique path.
```

s3 버킷에 cors 추가하면 문제 해결

```
[
    {
        "AllowedHeaders": [
            "Authorization"
        ],
        "AllowedMethods": [
            "GET"
        ],
        "AllowedOrigins": [
            "*"
        ],
        "ExposeHeaders": [],
        "MaxAgeSeconds": 3000
    }
]
```
