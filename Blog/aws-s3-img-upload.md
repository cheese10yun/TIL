## AWS S3 업로드시 이미지 최적화
AWS S3 이미지 업로드시 이미지 최적화를 진행

## 필수 패키지 설치
```
npm install --save async
npm install --save aws-sdk
npm install --save imagemin
npm install --save imagemin-pngquant
npm install --save formidable
```

## UploadService 설명

### formidable 업로드
```javascript
Upload.formidable = (req, callback) => {
  let _fields;

  form.parse(req, function (err, fields) {
    _fields = fields;
  });

  form.on('error', function (err) {
    callback(err, null, null);
  });

  form.on('end', function () {
    callback(null, this.openedFiles, _fields);
  });
};
```

* `form.on('error')` formidable 업로드 중 오류 발생시 `callback`으로 `err` 전달
* `form.on('end')` formidable 업로드가 완료되면 `callback`으로 파일정보와, 필드값 전달

### 이미지 최적화
```javascript
Upload.optimize = (files, callback) => {
  async.each(files, (file, cb) => {
    imagemin([file.path], `${ROOT_PATH}/temp/`, {
      plugins: [
        imageminPngquant({quality: '0-80', verbose: false, floyd: 1})
      ]
    }).then(() => {
      cb();
    })
  }, (err) => {
    callback(err)
  });
};
```
* `async.each`으로 업로드할 파잇의 이미지 최적화 작업진행
* `imagemin(최적화할 이미지, 최적화 이후 저장될 이미지 경로, 이미지 최적화 작업)`
* 이미지 최적화 플러그인 `imageminPngquant` 사용

### imageminPngquant 플러그인

* `floyd` 이미지 디더링 작업 사용 `Type: boolean, Default: false`
* `quality` 이미지 퀄리 지정 사용 `Type: string` 0~100 사용 가능
* `verbose` 불필효한 메타정보 제거 사용 `Type: boolean, Default: false`

### S3 업로드
```javascript
Upload.s3 = (files, key, callback) => {
  async.each(files, (file, cb) => {
    params.Key = key + file.name;
    params.Body = require('fs').createReadStream(file.path);

    S3.upload(params, (err, result) => {
      cb(err, result);
    });
  }, (err, result) => {
    callback(err, result);
  });
};
```
* `async.each`으로 업로드할 파잇의 갯수만큼 S3에 업로드
* `files` 업로드할 파일들의 정보
* `key` S3에 업로드될 경로
* `err, result` 값을 `callback` 으로 전달 에러가 발생하면 S3업로드 정지
* [Yun Blog Node AWS S3 업로드](https://cheese10yun.github.io/Node-AWS-S3-Upload) 자세한 설명은 참고


## router 에서 사용법
```javascript
router.post('/upload', (req, res) => {
  const tasks = [
    (callback) => {
      Upload.formidable(req, (err, files, fields) => {
        callback(err, files, fields);
      });
    },
    (files, fields, callback) => {
      Upload.optimize(files, (err) => {
        callback(err, files, fields);
      });
    },
    (files, fields, callback) => {
      Upload.s3(files, 'channel/test/', (err, result) => {
        callback(err, result)
      });
    }
  ];
  async.waterfall(tasks, (err, result) => {
    if (!err) {
      res.json({success: true, msg: '업로드 성공'})
    } else {
      res.json({success: false, msg: '업로드 실패'})
    }
  });
});
```

### tasks 작업은 UploadService 모듈로 진행

* `formidable` 메소드로 이미지 업로드 진행
* `optimize` 메소드로 이미지 최적화 진행
* `s3` 모듈로 s3 업로드 진행
* `async.waterfall` 으로 위 작업 순차 진행


## 사이즈 비교

원본              | imageOptim App | imagemin
:-------------- | :------------- | :-------------
10,645,070 byte | 8,499,904 byte | 2,858,674 byte |
473,459 byte    | 282,029 byte   | 177,423 byte   |
421,698 byte    | 258,743 byte   | 189,266 byte   |
382,774 byte    | 232,684 byte   | 182,071 byte   |
467,184 byte    | 282,368 byte   | 164,739 byte   |

**imageOptim App은 맥에서 사용하는 이미지 최적화 툴입니다.**

* 이미지 사이즈(가로세로 크기)는 변경되지 않습니다.
* bit color 값은 8bit로 수정됩니다.
* 이미지에 대한 지식이 없어 비포 에프터 사진을 첨부했습니다.

### 원본 이미지
![](http://i.imgur.com/mx9UTs2.png)

### 최적화 이미지
![](http://i.imgur.com/4pEMLxw.png)
