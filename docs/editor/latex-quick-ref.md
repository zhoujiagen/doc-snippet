# Latex Quick Reference


## 工具


## 模板

### 单文档


!!! note "文件结构"
    ```
    ├── image
    │   └── hdfs-write-data.png
    ├── references.bib
    └── template-ariticle.tex
    ```

!!! note "template-ariticle.tex"
    ``` latex
    \documentclass{article}

    \usepackage{xeCJK}
    % 段落缩进
    \usepackage{indentfirst}
    % 参考文献
    \bibliographystyle{alpha}
    % 链接参考文献和引用
    \usepackage{hyperref}
    % 索引
    \usepackage{makeidx}
    \makeindex
    % 图片
    \usepackage{graphicx}
    % 标题
    \usepackage{caption}
    \captionsetup{figurename=图, tablename=表}
    % 页面尺寸
    \usepackage{geometry}
    \geometry{a4paper, left=2cm, right=4cm, top=2cm, bottom=2cm}
    % 程序代码
    \usepackage{listings}
    % 列表
    \usepackage{enumitem}

    \title{文章标题}
    \author{周加根(zhoujiagen@gmail.com)}
    %\date{2016-02-19}

    % 在Mac应用>字体册.app中查看
    %\setCJKmainfont{Lantinghei SC Extralight}
    \setCJKmainfont[BoldFont={STHeiti}]{Lantinghei SC Extralight}
    \begin{document}
    % 段落间距
    \setlength{\parskip}{0.5em}

    \maketitle

    \renewcommand\abstractname{简述}
    \begin{abstract}
    % 段落间距
    \setlength{\parskip}{0.5em}
    文章的简述
    \end{abstract}

    \newpage

    \renewcommand\contentsname{目录}
    \tableofcontents

    \newpage

    \section{Section 1}

    Section 1 Content.

    % 列表
    \begin{enumerate}[itemsep=0pt,parsep=0pt,label=(\arabic*)]
    \item[(1)] item1

    item1 content.

    \item[(2)] item2

    item2 content.

    \end{enumerate}

    \subsection{SubSection 1}

    SubSection content.

    \newpage
    \section{Section 2}

    % 代码字体
    \texttt{int main() {}}

    % 大段代码
    \lstset{ basicstyle=\ttfamily, keywordstyle=\bfseries, commentstyle=\ttfamily, numbers=right, numberstyle=\footnotesize }
    \begin{lstlisting}[language=C]
    	#include <stdio.h>
    	main() {
    	    printlf("Hello world.\n");
    	}
    \end{lstlisting}

    % 索引
    \index{HDFS}

    % 文献引用
    \cite{frontiers-mda}

    % 交叉引用
    %\ref{subsubsec:reservewords}(P.\pageref{subsubsec:reservewords})

    % 图片和图片引用
    HDFS中文件写入步骤见图\ref{fig:hdfs-write-data}:

    \begin{figure}
    \centering
    \includegraphics[scale=0.5]{image/hdfs-write-data.png}
    \caption{HDFS数据写入}\label{fig:hdfs-write-data}
    \end{figure}


    \newpage
    \renewcommand\refname{参考文献}
    \bibliography{references}

    \newpage
    \renewcommand\indexname{索引}
    \printindex

    \end{document}
    ```

!!! note "references.bib"
    ``` BibTex
    %% This BibTeX bibliography file was created using BibDesk.
    %% http://bibdesk.sourceforge.net/

    %% Created for zhang at 2017-02-19 22:05:32 +0800


    %% Saved with string encoding Unicode (UTF-8)



    @book{frontiers-mda,
    	Date-Added = {2017-02-19 12:29:58 +0000},
    	Date-Modified = {2017-02-19 14:05:19 +0000},
    	Editor = {美国国家学术院国家研究委员会},
    	Keywords = {massive dataset, data analysis},
    	Publisher = {清华大学出版社},
    	Title = {Frontiers in Massive Data Analysis},
    	Year = {2015}}
    ```



## 文档组件

### 转义字符

[The following ten characters have special meanings in (La)TeX](https://tex.stackexchange.com/questions/34580/escape-character-in-latex):

```
& % $ # _ { } ~ ^ \
```

### 段落

```
\newpage
\section{Section 1}
\subsection{SubSection 1}
```

### 代码

代码字体:

``` latex
\texttt{int main() {}}
```

大段代码

``` latex
\lstset{ basicstyle=\ttfamily, keywordstyle=\bfseries, commentstyle=\ttfamily, numbers=left, numberstyle=\footnotesize }
\begin{lstlisting}[language=C]
    #include <stdio.h>
    main() {
        printlf("Hello world.\n");
    }
\end{lstlisting}
```

### 索引词

``` latex
\index{HDFS}
```

### 引用

#### 文献引用

``` latex
\cite{frontiers-mda}
```

#### 图片引用

``` latex
HDFS中文件写入步骤见图\ref{fig:hdfs-write-data}:

\begin{figure}
\centering
\includegraphics[scale=0.5]{image/hdfs-write-data.png}
\caption{HDFS数据写入}\label{fig:hdfs-write-data}
\end{figure}
```

#### 章节引用

``` latex
%\ref{subsubsec:reservewords}(P.\pageref{subsubsec:reservewords})
```

### 表格

- [LaTeX Tables Generator](https://www.tablesgenerator.com/): 在线的Latex表格生成器

``` latex
\begin{table}[]
\begin{tabular}{ll}
Title & Content \\
1     & 2       \\
3     & 4       \\
5     & 6
\end{tabular}
\end{table}
```

### 列表

``` latex
\begin{enumerate}[itemsep=0pt,parsep=0pt,label=(\arabic*)]
\item item1

item1 content.

\item item2

item2 content.

\end{enumerate}
```

### 脚注

``` latex
I'm writing something here to test \footnote{footnotes working fine} several features.
```

``` latex
You can write the footnote text\footnotemark in its own line.
\footnotetext{Second footnote}
```
