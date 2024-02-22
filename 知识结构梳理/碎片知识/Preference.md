1. 修改CapsLock和Ctrl映射:在计算机\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Keyboard Layout 下新建ScanCode Map 填入:
		00 00 00 00 00 00 00 00
		03 00 00 00 1D 00 3A 00
		3A 00 1D 00 00 00 00 00
2. 多屏协同:duet
3. Debug:
	1. DebugView
4. APK工具:
	1. apktool
		1. 拆包:%APK_TOOL% -r d a.apk
		2. 封包:%ROOT%apktool.bat b a
	2. jarsigner
			签名：jarsigner.exe -verbose -keystore fgo.jks -signedjar %OUT_APK% %OUT_APK% fgo
1. VsVim配置:
	```
	set vsvim_useeditordefaults
	set backspace=indent,eol,start
	set clipboard=unnamed
	set ignorecase
	nmap <space>/ :vsc Edit.FindinFiles<CR>
	nmap <space><space> :vsc Edit.GoToFile<CR>
	nmap <C-O> :vsc View.NavigateBackward<CR>
	nmap <C-I> :vsc View.NavigateForward<CR>
	nmap <S-H> :vsc Window.PreviousTab<CR>
	nmap <S-L> :vsc Window.NextTab<CR>
	```
	