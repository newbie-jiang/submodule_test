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

## 2. **添加 stm32h7rsxx_hal_driver 作为子模块**

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

上述中我使用 git clone --recursive <我的git仓库地址>  拉取了源码，现在我新添加了一些文件进去，git add .   再提交上传  会将子模块的代码也传输上去么？我不希望上传子模块

## 1. **`git add .` 会不会上传子模块代码？**

### **答案：不会上传子模块里的源码到主仓库！**

- 当你在主仓库执行 `git add .`，**子模块（如 `third_party/fatfs/`）目录里的源码不会被直接提交到主仓库**。
- 主仓库实际**只记录“子模块当前指向的 commit hash”**，而不会把子模块的全部源码纳入你的主仓库历史。
- **只有你真的在子模块目录里`git add`并push到子模块仓库，才会改变子模块仓库的内容。**
- 你主仓库只要`git add .`，再`git commit`，上传的是你主工程的变更，和子模块源码**无关**。

------

## 2. **什么时候会有“子模块变化”被记录？**

- **如果你没有进入子模块目录去修改、pull、checkout、commit等操作**，那么主仓库里的子模块引用也不会变。
- **如果你切换了子模块的分支或者commit**，主仓库会记录下“fatfs现在指向了哪个commit”，
   但**这只是一个“指针”，不是子模块内容本身**。

------

## 3. **结论**

> **用`git add .`和`git commit`，不会把submodule的源码传到主仓库。只会提交你主仓库的新文件/改动。如果你没动过submodule指针，连“指针变更”都不会记录。**



