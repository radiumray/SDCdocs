
## installStash.py
```py
import requests as r; exec(r.get('https://raw.githubusercontent.com/ywangd/stash/master/getstash.py').text)

```

## pythonistaFTP.py
```py

'''FTP server for Pythonista (iOS)

You can use this to exchange files with a Mac/PC or a file management app on the same device (e.g. Transmit).

If you use a Mac, you can connect from the Finder, using the "Go -> Connect to Server..." menu item.
'''

import os
from socket import gethostname

def install_pyftpdlib():
	print 'Downloading pyftpdlib...'
	import urllib
	import shutil
	os.chdir(os.path.expanduser('~/Documents'))
	urllib.urlretrieve('https://pypi.python.org/packages/source/p/pyftpdlib/pyftpdlib-1.4.0.tar.gz', 'pyftpd.tar.gz')
	import tarfile
	t = tarfile.open('pyftpd.tar.gz')
	t.extractall()
	shutil.copytree('pyftpdlib-1.4.0/pyftpdlib', 'site-packages/pyftpdlib')
	shutil.rmtree('pyftpdlib-1.4.0')
	os.remove('pyftpd.tar.gz')
	
try:
	import pyftpdlib
except ImportError:
	install_pyftpdlib()

from pyftpdlib.authorizers import DummyAuthorizer
from pyftpdlib.handlers import FTPHandler
from pyftpdlib.servers import FTPServer
import threading

def main():
	authorizer = DummyAuthorizer()
	authorizer.add_anonymous(os.path.expanduser('~/Documents'), perm='elradfmwM')
	handler = FTPHandler
	handler.authorizer = authorizer
	server = FTPServer(('0.0.0.0', 21), handler)
	t = threading.Thread(target=server.serve_forever)
	t.start()
	print 'Server started.'
	print '\nConnect as guest/anonymous user to ftp://localhost:21 (from this device) or "ftp://(YOUR_IP_ADDRESS):21" (from other devices in your network -- you can find the IP address of your device in the WiFi settings)'
	try:
		while True: pass
	except KeyboardInterrupt:
		server.close_all()
		print 'Server stopped'

if __name__ == '__main__':
	main()


```


## you-get.py

```py

#!/usr/bin/env python3
from __future__ import unicode_literals
from ctypes import c_void_p, c_char_p, cdll, util
import re, appex, console, os
import clipboard
import sys
from you_get import common as you_get
# Load Objective-C runtime:
objc = cdll.LoadLibrary(util.find_library('objc'))
objc.sel_getName.restype = c_char_p
objc.sel_getName.argtypes = [c_void_p]
objc.sel_registerName.restype = c_void_p
objc.sel_registerName.argtypes = [c_char_p]
objc.objc_getClass.argtypes = [c_char_p]
objc.objc_getClass.restype = c_void_p

def msg(obj, restype, sel, argtypes=None, *args):
	if argtypes is None:
		argtypes = []
	objc.objc_msgSend.argtypes =  [c_void_p, c_void_p] + argtypes
	objc.objc_msgSend.restype = restype
	res = objc.objc_msgSend(obj, objc.sel_registerName(sel), *args)
	return res
	
def nsstr(s):
	return msg(objc.objc_getClass('NSString'), c_void_p, 'stringWithUTF8String:', [c_char_p], s)
	
def save_video(video_file):
	uikit = cdll.LoadLibrary(util.find_library('UIKit'))
	save_func = uikit.UISaveVideoAtPathToSavedPhotosAlbum
	save_func.argtypes = [c_void_p] * 4
	save_func(nsstr(video_file), None, None, None)

def my_hook(d):
	if d['status'] == 'finished':
		console.hide_output()
		chosen = console.alert('Download Finished', "Video is already in Pythonista.\nWaht else do you want to do with it?", 'Quick Look', 'Open in', 'Save to Album')
		if chosen == 1:
			console.quicklook(d['filename'])
		elif chosen == 2:
			console.open_in(d['filename'])
		elif chosen == 3:
			save_video(d['filename'].encode('utf-8'))
			
if appex.is_running_extension() and re.search('https*:\/\/[^\s]+', appex.get_attachments()[0]) is not None:
	url = appex.get_attachments()[0]
else:
	clip = re.search('https*:\/\/[^\s]+', clipboard.get())
	if clip is None:
		url = console.input_alert('URL Input')
	else:
		url = clipboard.get()

console.clear()

config = input('input config: ')
sys.argv=['you-get','%s'%config,'%s'%url]
you_get.main()


```
