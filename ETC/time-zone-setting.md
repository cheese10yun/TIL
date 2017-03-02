# time zone setting

##

```
$ tzselect

Please identify a location so that time zone rules can be set correctly.
Please select a continent or ocean.
 1) Africa
 2) Americas
 3) Antarctica
 4) Arctic Ocean
 5) Asia
 6) Atlantic Ocean
 7) Australia
 8) Europe
 9) Indian Ocean
10) Pacific Ocean
11) none - I want to specify the time zone using the Posix TZ format.
```

해당 대륙 선택 : 한국 5

```
Please select a country.
 1) Afghanistan		  18) Israel		    35) Palestine
 2) Armenia		  19) Japan		    36) Philippines
 3) Azerbaijan		  20) Jordan		    37) Qatar
 4) Bahrain		  21) Kazakhstan	    38) Russia
 5) Bangladesh		  22) Korea (North)	    39) Saudi Arabia
 6) Bhutan		  23) Korea (South)	    40) Singapore
 7) Brunei		  24) Kuwait		    41) Sri Lanka
 8) Cambodia		  25) Kyrgyzstan	    42) Syria
 9) China		  26) Laos		    43) Taiwan
10) Cyprus		  27) Lebanon		    44) Tajikistan
11) East Timor		  28) Macau		    45) Thailand
12) Georgia		  29) Malaysia		    46) Turkmenistan
13) Hong Kong		  30) Mongolia		    47) United Arab Emirates
14) India		  31) Myanmar (Burma)	    48) Uzbekistan
15) Indonesia		  32) Nepal		    49) Vietnam
16) Iran		  33) Oman		    50) Yemen
17) Iraq		  34) Pakistan
```

해당 국가 선택 : 한국 23

```
The following information has been given:

	Korea (South)

Therefore TZ='Asia/Seoul' will be used.
Local time is now:	Fri Mar  3 02:16:34 KST 2017.
Universal Time is now:	Thu Mar  2 17:16:34 UTC 2017.
Is the above information OK?
1) Yes
2) No
```

1 입력

```
You can make this change permanent for yourself by appending the line
	TZ='Asia/Seoul'; export TZ
to the file '.profile' in your home directory; then log out and log in again.

Here is that TZ value again, this time on standard output so that you
can use the /usr/bin/tzselect command in shell scripts:
Asia/Seoul
```

`TZ='Asia/Seoul'; export TZ` 부분 `.profile`에 마지막 부분에 입력 `.profile`이 없는 경우 `.bash_profile`에 입력

```
$ cd ~/
$ vi .profile or .bash_profile
```
