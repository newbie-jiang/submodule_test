submodule使用方法



## 1. **创建你的主工程仓库（假设用 GitHub）**

```
# 新建目录
mkdir mytest
cd mytest

# 初始化git仓库
git init

# 新建main.c
echo 'int main() { return 0; }' > main.c

# 添加并提交
git add main.c
git commit -m "init: main.c"
```

------

## 2. **添加 FatFs 作为子模块**

你可以用 [stm32h7rsxx_hal_driver 官方 GitHub 仓库](https://github.com/STMicroelectronics/stm32h7rsxx_hal_driver.git)：



```
git submodule add https://github.com/STMicroelectronics/stm32h7rsxx_hal_driver.git hal/driver
git commit -m "chore: add stm32h7rsxx_hal_driver as submodule"
```

这两步做了什么？

- 会自动在项目里创建 `third_party/fatfs` 目录（实际是个子模块指针）。
- 会在主目录生成或修改 `.gitmodules` 文件，记录子模块的信息。

------

## 3. **推送到你的远程仓库（如GitHub）**

```
git remote add origin <你的git仓库地址>
git push -u origin master
```

------

## 4. **测试 clone 是否自动拉取 FatFs 子模块**

**在任意一台新电脑/新目录里这样clone：**

```
git clone --recursive <你的git仓库地址>
# 或者
git clone <你的git仓库地址>
cd <你的仓库名>
git submodule update --init --recursive
```

**此时会看到：**

- `main.c` 在主目录
- `third_party/fatfs` 下有 fatfs 全部源码
- `.gitmodules` 记录了子模块信息

------

## 5. **实际仓库结构**

```
mytest/
  |-- main.c
  |-- third_party/
      |-- fatfs/         # 这是submodule（clone后自动填充fatfs源码）
  |-- .gitmodules        # 记录submodule信息
  |-- .git/              # git目录
```

------

## 6. **关键点总结**

- **务必保证 `.gitmodules` 和 `third_party/fatfs` 都被add和commit。**
- 子模块仅记录“指向fatfs仓库的commit指针”，源码不会和主仓库混合在一起。
- clone新环境只需 `--recursive`，无需手动再去拉 fatfs。

------

## 7. **如果要指定FatFs版本（可选）**

```
cd third_party/fatfs
git checkout R0.14b  # 换成你需要的tag或commit
cd ../..
git add third_party/fatfs
git commit -m "fix: use FatFs R0.14b"
```

这样可以确保子模块永远指向你想要的FatFs版本。

------

## **一条命令测试效果**

1. 完成上述步骤后，别人这样拉取即可：

   ```
   git clone --recursive <你的仓库地址>
   ```

------

