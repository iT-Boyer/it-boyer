+++
title = "在 osx 终端如何移动大文件？"
description = "博客简介"
date = 2023-06-02T19:14:00+08:00
draft = false
authors = ["iTBoyer"]
password = ""
+++

在 osx 终端如何使用 mv 命令移动大文件，遇到的问题：mv: fastcopy: write() failed: /Volumes/AIGO/file File too large 

当在Mac OSX终端使用mv命令移动大文件时,你可能会遇到"mv: fastcopy: write() failed: /Volumes/AIGO/file File too large"这样的错误。这是因为mv命令在后台使用fastcopy工具来高效移动文件,但fastcopy无法移动超过4GB的文件。 

要解决这个问题,你有几个选项: 

1.  使用ditto命令代替mv。ditto是Mac自带的专用于高效移动大文件和文件夹的命令。你可以使用:

\`\`\`bash ditto /Volumes/AIGO/file /destination/path \`\`\` 

来移动文件。 

1.  将文件切片为4GB以下的大小,逐个移动,然后重新组合。你可以使用:

\`\`\`bash split -b 4g /Volumes/AIGO/file mv /Volumes/AIGO/file.\* /destination/path cat /destination/path/file.\* &amp;gt; /destination/path/file rm /destination/path/file.\* \`\`\` 

1.  安装特定用于高效移动大文件的工具,如FastCopy或rsync。例如:

\`\`\`bash brew install fastcopy fastcopy /Volumes/AIGO/file /destination/path \`\`\` 

或者 

\`\`\`bash rsync -av /Volumes/AIGO/file /destination/path \`\`\` 

1.  临时将文件压缩为.zip或.gz文件,移动压缩文件,然后解压。例如:

\`\`\`bash zip -r file.zip /Volumes/AIGO/file mv file.zip /destination/path unzip /destination/path/file.zip \`\`\` 

这些都是移动Mac OSX上的大文件的可选方法。ditto和专门的文件移动工具可能是首选方法,因为无需重复的压缩和解压缩步骤。 

请让我知道如果你对在Mac OSX上移动大文件有任何其他问题! 

