---
title: 并行格式化代码的Python脚本模板
category_bar: true
math: false
tags: [clang-format, Python]
category: [Python]
date: 2026-02-26 19:30:34
---

由于经常遇到批量格式化代码的需求，因此保存一个使用Python编写的脚本模板，用于并行格式化指定目录下的代码文件。脚本使用`clang-format`作为格式化工具，并支持自定义文件扩展名、目标目录和排除目录。clang-format可以通过[**LLVM**](https://github.com/llvm/llvm-project/releases)或**pip**方式安装。

在格式化过程中，clang-format会对每个文件逐级向上查找`.clang-format`配置文件，直到找到为止。如果没有找到，则使用默认配置。可以在项目根目录或子目录中放置`.clang-format`文件来指定格式化规则。


```python
import subprocess
import sys
from pathlib import Path
from concurrent.futures import ThreadPoolExecutor, as_completed

# 替换为实际需要格式化的文件扩展名列表
EXTS = frozenset({'.c', '.cpp', '.cxx', '.h', '.hpp', '.hxx'})
# 要格式化文件所在的目录。默认值为脚本所在目录：Path(__file__).resolve().parent
# 可选：替换为实际的项目根目录路径
ROOT = Path(__file__).resolve().parent
# 替换为实际的目标目录列表
TARGET_DIRS = ('include/subdir1', 'src/subdir2')
# 替换为实际的要排除的目录列表
EXCLUDE_DIRS = frozenset({'subdir3', 'subdir4'})

def main(argv=None):
    argv = argv if argv is not None else sys.argv
    # 可选：自定义 clang-format 可执行文件路径（作为默认值使用）
    # 要使用特定版本，请使用对应的绝对路径或直接通过命令行参数传入替代路径
    # CF_PATH = r"D:\Python\python314\Scripts\clang-format.exe"  # 使用绝对路径
    CF_PATH = r"clang-format"  # 使用环境变量中的路径
    clangformat = argv[1] if len(argv) > 1 else CF_PATH
    # 也可以通过命令行参数传入clang-format路径，例如：
    #   python format.py D:\Python\python314\Scripts\clang-format.exe

    try:
        subprocess.run([clangformat, '--version'], check=True)
    except (FileNotFoundError, subprocess.CalledProcessError):
        print(f"clang-format not found or failed at: {clangformat}")
        sys.exit(1)

    # 收集所有待处理文件
    files = []
    for subdir in TARGET_DIRS:
        pdir = ROOT / subdir
        if not pdir.exists():
            continue
        for p in pdir.rglob('*'):
            if any(part in EXCLUDE_DIRS for part in p.parts):
                continue
            if p.suffix.lower() in EXTS and p.is_file():
                files.append(p)

    # 单文件格式化函数
    def _format_one(p):
        try:
            proc = subprocess.run([clangformat, str(p)], stdout=subprocess.PIPE, stderr=subprocess.PIPE, check=False)
        except FileNotFoundError:
            print(f"clang-format not found: {clangformat}")
            return False
        if proc.returncode != 0:
            err = proc.stderr.decode('utf-8', errors='replace').splitlines()[0] if proc.stderr else ''
            print(f"Error formatting {p}: {err}")
            return False
        formatted = proc.stdout

        original = p.read_bytes()
        if formatted != original:
            p.write_bytes(formatted)
            print(f"Formatted: {p}")
            return True
        return False

    # 并行执行格式化
    count = 0
    if files:
        with ThreadPoolExecutor() as ex:
            futures = {ex.submit(_format_one, p): p for p in files}
            for fut in as_completed(futures):
                try:
                    if fut.result():
                        count += 1
                except Exception as e:
                    print(f"Exception formatting {futures[fut]}: {e}")
    print(f"[=OK=] Formatted {count} files.")


if __name__ == "__main__":
    main()
```