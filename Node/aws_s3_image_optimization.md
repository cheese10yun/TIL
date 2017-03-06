## AWS S3 업로드시 이미지 최적화
AWS S3 이미지 업로드시 이미지 최적화를 진행하고 업로드하는 것이 브라우저에서의 속도가 크게 도움이 됩니다. `formidable` 업로드 , `AWS-S3` 업로드는 이 전 포스팅에서 한번 다뤘기 때문에 간단하게 설명하고 이미지 최적화 적업에 대해서 자세히 포스팅하겠습니다.

## 작업순서
1. `formidable` 모듈로 이미지 업로드 진행
2. `imagemin` 모듈로 업로드된 이미지 최적화 진행
3. `aws-sdk` 모듈을로 최적화 작업이 완료된 이미지 S3에 업로드

## 필수 패키지 설치
```
npm install --save async
npm install --save aws-sdk
npm install --save imagemin
npm install --save imagemin-pngquant
npm install --save formidable
```

## UploadService.js 설명

### formidable 모듈을 이용한 이미지 업로드
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
* `form.on('end')` formidable 업로드가 오류 없이 완료되면 `callback`으로 파일정보와, 필드값 전달

### imagemin 모듈을 이용한 이미지 최적화
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
* `async.each`으로 업로드할 파일의 개수만큼 이미지 최적화 적업 진행
* `imagemin([최적화할 이미지 경로(배열 타입이여야함)], 최적화 이후 저장될 이미지 경로, 이미지 최적화 작업)`
* 위의 예제는 업로된 경로와 최적화가 이루어지는 경로가 동일하여 덮어쓰여 집니다.
* 이미지 최적화 플러그인 `imageminPngquant` 사용

### imageminPngquant 플러그인

```javascript
plugins: [
	imageminPngquant({quality: '0-80', verbose: false, floyd: 1})
]
```
* `floyd` 이미지 디더링 작업 사용 `Type: boolean, Default: false`
* `quality` 이미지 퀄리 지정 사용 `Type: string` 0~100 사용 가능
* `verbose` 불필효한 메타정보 제거 사용 `Type: boolean, Default: false`
* `imageminPngquant` 의 다양한 속성은 [imageminPngquant](https://www.npmjs.com/package/imagemin-pngquant) 에서 확인 할 수 있습니다.
* `imageminPngquant` 플러그인 이외에도 다양한 플러그인을 사용해서 이미지에 대한 다양한 작업들을 진행할 수 있습니다.

### 최적화 완료된 이미지 S3 업로드
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
* `async.each`으로 업로드할 파잇의 개수만큼 S3에 업로드 작업 진행
* `files` 업로드할 파일들의 정보
* `params.Key` S3에 업로드 될 경로와 파일이름을 지정합니다.
* `params.Body` 이미지 최적화 작업이 끝난 파일의 경로를 입력합니다.
* `S3.upload(...)` 실질적인 S3 이미지 업로드가 진행됩니다. `cb(err, result)`으로 에러가 발생하면 즉시 정지하고, 에러가 발생하지 않으면 파일의 개수만큼 업로드를 반복합니다.
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

* 위에서 작성한 `UploadService.js` 모듈로 아래의 작업들이 진행됩니다.
* `formidable` 메소드로 이미지 업로드 진행
* `optimize` 메소드로 이미지 최적화 진행
* `s3` 메서드로 s3 업로드 진행
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

### 마무리

최근에 이미지 S3 업로드시 이미지 최적화 해야 할 작업이 있어서 코드를 만들고 간단하게 정리해보았습니다. 아직 프로덕션에 사용하는 코드는 아니라서 그렇게 안전한 코드는 아닌점... 미리 말씀드립니다. 이 플러그인 외에도 이미지 크롭, 이미지 해상도 조정 등 다양한 플러그인들이 많아 좀 더 검색해보시고 적용하시면 보다 좋을 거 같습니다. 부디 도움이 조금이라도 되셨기를 바랍니다.
