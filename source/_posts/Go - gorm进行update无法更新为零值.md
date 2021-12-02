---
title: Go - gorm进行update无法更新为零值
abbrlink: 108989d9
date: 2021-12-02 16:10:55
categories:
  - Go
tags:
  - Go
  - Gorm
top_img: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/wallhaven-m9olo8.jpg
cover: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/wallhaven-m9olo8.jpg

---



gorm update无法更新零值，字段值无法改为0的解决办法（""好像有一样的问题，但没测试）

尝试了好几种方法，只有这种有效 也方便

将字段类型变为指针即可解决

```go
type Users struct {
	Uid               int64  `json:"uid" form:"uid"`
	Username          string `json:"username" form:"username"`
	Password          string `json:"password" form:"password"`
	Phone             string `json:"phone" form:"phone"`
	Email             string `json:"email" form:"email"`
	Nickname          string `json:"nickname" form:"nickname"`
	NicknameUpdated   int    `json:"status" form:"nickname_updated"`
	AvatarFile        string `json:"avatar_file" form:"avatar_file"`
	Gender            *int   `json:"gender" form:"gender"`
	Birthday          string `json:"birthday" form:"birthday"`
	BirthdayUpdatedAt string `json:"birthday_updated_at" form:"birthday_updated_at"`
}
```

//修改时

```go
func EditUserProfileById(uid int64, nickname string, nicknameUpdated int, birthday, birthdayUpdatedAt string, gender int) bool {
	conn := db.GetConn()

	var user Users
	user.Gender = &gender
	
	fmt.Println(user)
	//注意注意  更新0值使用Updates不能再传指针了 必须是值传递
	conn.Table("users").Where(" `uid` = ?", uid).Update(user)
	return true
}
```













