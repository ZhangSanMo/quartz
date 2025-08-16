常常会用到`Path(__file__).parent`来获取当前文件所在文件夹的路径, 所以`Path(__file__).parent.parent`一定是文件所在文件夹的父文件夹吧!

# No!

实际上`Path(__file__).parent`与当前命令位置有关.
举个例子, 假定文件路径为`/a/b/c/d/f.py`
1. 当前shell位置为`/a/b/c/d`
	此时的`Path(__file__).parent`为`.`,
	对, 就是当前位置. 而`.parent`属性是纯粹按语法解析的, 所以解析`Path(__file__).parent.parent`的结果还是`.`
2. 当前shell位置为`/a/b/c/
	此时的`Path(__file__).parent`为`d`, 解析`Path(__file__).parent.parent`的结果还是`.`, 也就是shell位置



