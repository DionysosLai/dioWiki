# E 盘图片文件扫描报告

- 扫描根目录：`E:\`
- 生成时间：2026-04-30 15:09:59
- 图片校验方式：文件头签名校验（未安装 Pillow）
- 跳过目录：$RECYCLE.BIN, Config.Msi, Program Files, Program Files (x86), Recovery, System Volume Information, Windows

## 总览

| 指标 | 结果 |
|---|---:|
| 图片类文件数量 | 68253 |
| 图片类文件总体积 | 52.95 GB |
| 无效/疑似损坏图片 | 2095 |
| 精确重复图片组 | 6138 |
| 可释放空间（每组保留 1 份估算） | 2.29 GB |
| 同名图片组（不代表内容重复） | 6409 |
| 访问错误数量 | 0 |

## 输出文件

- 精确重复图片：`E_drive_duplicate_images.csv`
- 无效/疑似损坏图片：`E_drive_invalid_images.csv`
- 同名图片清单：`E_drive_same_name_images.csv`
- 大文件 Top200：`E_drive_large_images_top200.csv`

## 扩展名统计

| 扩展名 | 数量 | 总大小 |
|---|---:|---:|
| `.jpg` | 10940 | 35.77 GB |
| `.png` | 54044 | 11.76 GB |
| `.heic` | 1852 | 3.87 GB |
| `.tif` | 101 | 1.33 GB |
| `.bmp` | 761 | 130.73 MB |
| `.jpeg` | 41 | 57.92 MB |
| `.dng` | 12 | 9.52 MB |
| `.ico` | 132 | 6.47 MB |
| `.svg` | 82 | 4.84 MB |
| `.gif` | 166 | 4.65 MB |
| `.tiff` | 113 | 2.23 MB |
| `.raw` | 6 | 131.75 KB |
| `.webp` | 3 | 1.27 KB |

## 清理建议

1. 优先查看 `E_drive_duplicate_images.csv` 中 `action_suggestion=DELETE_CANDIDATE` 的行，这些是 SHA256 完全一致的重复文件，风险最低。
2. 再查看 `E_drive_invalid_images.csv`，确认是否为真正损坏、空文件或误改扩展名的文件。
3. `E_drive_same_name_images.csv` 只代表文件名相同，不代表内容重复，请不要直接批量删除。
4. 建议先把待删文件移动到临时回收目录，确认无误后再删除。
5. 本次扫描只读，不会删除或移动任何文件。
