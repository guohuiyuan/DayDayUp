# go语言写算法题常用语法
1. 插入一个元素
```
a = append(a, 0)     // 切片扩展1个空间
copy(a[i+1:], a[i:]) // a[i:]向后移动1个位置
a[i] = x             // 设置新添加的元素
```
2. 插入切片
```
a = append(a, x...)       // 为x切片扩展足够的空间
copy(a[i+len(x):], a[i:]) // a[i:]向后移动len(x)个位置
copy(a[i:], x)            // 复制新添加的切片
```
3. go语言去重
因为go切片没有查询重复元素的方法，所以去重一般是遍历或者建一个map
建map
```
// 以下是把medal的数组插在vups前列，同时删除vups中和medal重复的元素
type vup struct {
	Mid    int64  `gorm:"column:mid;primary_key"`
	Uname  string `gorm:"column:uname"`
	Roomid int64  `gorm:"column:roomid"`
}

type medal struct {
	Uname     string `json:"target_name"`
	medalInfo `json:"medal_info"`
}

type medalInfo struct {
	Mid              int64  `json:"target_id"`
	MedalName        string `json:"medal_name"`
	Level            int64  `json:"level"`
	MedalColorStart  int64  `json:"medal_color_start"`
	MedalColorEnd    int64  `json:"medal_color_end"`
	MedalColorBorder int64  `json:"medal_color_border"`
}
func main(){
		frontVups := make([]vup, 0)
        medalMap := make(map[int64]medal)
        for _, v := range medals {
            up := vup{
                Mid:   v.Mid,
                Uname: v.Uname,
            }
                frontVups = append(frontVups, up)
                medalMap[v.Mid] = v
            }
            vups = append(vups, frontVups...)
            copy(vups[len(frontVups):], vups)
            copy(vups, frontVups)
            for i := len(frontVups); i < len(vups); i++ {
                if _, ok := medalMap[vups[i].Mid]; ok {
                vups = append(vups[:i], vups[i+1:]...)
                i--
            }
        }
}			
```