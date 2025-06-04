This works for me to fix all the errors

### Install system dependencies

```
sudo apt install -y build-essential git libdistorm3-dev yara libraw1394-11 libcapstone-dev capstone-tool tzdata
```

### Install pip for Python 2

```
sudo apt install -y python2 python2.7-dev libpython2-dev
```

```
curl https://bootstrap.pypa.io/pip/2.7/get-pip.py --output get-pip.py
```

```
sudo python2 get-pip.py 
```

```
sudo python2 -m pip install -U setuptools wheel
```


### Install Volatility 2 and its Python dependencies


```
python2 -m pip install -U distorm3 yara pycrypto pillow openpyxl ujson pytz ipython capstone 
```

```
sudo python2 -m pip install yara
```

```
sudo ln -s /usr/local/lib/python2.7/dist-packages/usr/lib/libyara.so /usr/lib/libyara.so 
```

```
python2 -m pip install -U git+https://github.com/volatilityfoundation/volatility.git
```



 **References:**
 
-  https://seanthegeek.net/1172/how-to-install-volatility-2-and-volatility-3-on-debian-ubuntu-or-kali-linux/


*Thaylon Roberto Muniz da Silva, WorldSkills Lyon 2024, 06/03/2024.*