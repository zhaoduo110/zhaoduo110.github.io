---
title: Go - Gin支持文件下载
categories:
  - Go
tags:
  - Go
  - Gin
top_img: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218938852.jpg
cover: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218938852.jpg
abbrlink: 4f8085a1
date: 2021-10-20 18:24:33
---

## 路由配置

```go
//文件下载
router.GET("/download", DownloadFileService)
```

## 控制层

```go
/**
下载文件
*/
func DownloadFileService(c *gin.Context) {
	fileDir := "./static/export/"
	fileName := c.Query("file")
	//打开文件
	fileSource, errByOpenFile := os.Open(fileDir + fileName)
	//非空处理
	if helper.IsEmpty(fileDir) || helper.IsEmpty(fileName) || errByOpenFile != nil {
		/*c.JSON(http.StatusOK, gin.H{
		    "success": false,
		    "message": "失败",
		    "error":   "资源不存在",
		})*/
		c.Redirect(http.StatusFound, "/404")
		return
	}
	c.Header("Content-Type", "application/octet-stream")
	c.Header("Content-Disposition", "attachment; filename="+fileName)
	c.Header("Content-Transfer-Encoding", "binary")
	c.File(fileDir + "/" + fileName)
	//一定要关闭文件  否则文件资源会一直被占用
	fileSource.Close()

	return
}
```

## 访问连接

```
www.xxxx.com/download?file=xxxx.csv
```

