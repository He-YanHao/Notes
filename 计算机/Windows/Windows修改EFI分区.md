# Windows 环境修改
## 以管理员身份打开命令提示符或 PowerShell
- 按 `Win + X` → 选择“终端（管理员）”或“命令提示符（管理员）”。

## 挂载 EFI 分区
- 查看磁盘分区情况：
  ```cmd
  diskpart
  list disk
  select disk 0        （根据实际情况选择系统磁盘）
  list partition
  ```
  找到 EFI 分区（通常为 FAT32，大小约 100MB-500MB），记下分区号（例如 `partition 1`）。

- 分配驱动器盘符并挂载：
  ```cmd
  select partition 1
  assign letter=Z      （Z 可为其他未使用的盘符）
  exit
  ```

## 修改文件
- 现在 EFI 分区会以驱动器 `Z:` 显示（需在文件资源管理器中开启“显示隐藏文件”和“受保护的操作系统文件”）。
- 使用文件资源管理器或命令提示符修改文件（例如编辑 `\EFI\Microsoft\Boot\BCD` 可使用 `bcdedit` 工具，而非直接修改文件）。

## 卸载分区
  ```cmd
  diskpart
  select disk 0
  select partition 1
  remove letter=Z
  exit
  ```

