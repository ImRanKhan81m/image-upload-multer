# image-upload-multer

app.js
```
app.use("/api/v1/upload", imageUploadRoutes);

```

## imageUpload.route.js

```

const express = require("express");
const router = express.Router();
const imageUploadController = require("../controller/imageUpload.controller");
const uploader = require("../middlewere/uploader");

router.post(
  "/single-image-upload",
  uploader.single("image"),
  imageUploadController.fileUpload
);
router.post(
  "/multi-image-upload",
  uploader.array("image"),
  imageUploadController.multiFileUploads
);

module.exports = router;

```

##uploader.js this is middleawere

```
const multer = require("multer");
const path = require("path");

const storage = multer.diskStorage({
  destination: "image/",
  filename: (req, file, cb) => {
    const uniqueSuffix = Date.now() + "-" + Math.round(Math.random() * 1e9);
    cb(null, uniqueSuffix + "-" + file.originalname);
  },
});

const uploader = multer({
  storage,
  fileFilter: (req, file, cb) => {
    const supportedImage = /png|jpg|webp/;
    const extension = path.extname(file.originalname);
    if (supportedImage.test(extension)) {
      cb(null, true);
    } else {
      cb(new Error("Must be a png/jpg/webp image"));
    }
  },
  limits: {
    fileSize: 2000000,
  },
});

module.exports = uploader;

```

## single and multi imageUpload.controller.js 

```

exports.fileUpload = async (req, res) => {
  try {
    res.json({
      status: "success",
      url: `${process.env.multer_url}/${req.file.filename}`,
    });
  } catch (err) {}
};
exports.multiFileUploads = async (req, res) => {
  try {
    const imageUrl = [];
    req.files.forEach((img) => {
      imageUrl.push(`${process.env.multer_url}/${img.filename}`);
    });
    res.status(200).json({
      status: "success",
      imageURLs: imageUrl,
    });
  } catch (err) {}
};

```
