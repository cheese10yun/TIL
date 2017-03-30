# Bitbucket

## SSH Mac

```
$ ssh -v
usage: ssh [-1246AaCfGgKkMNnqsTtVvXxYy] [-b bind_address] [-c cipher_spec]
           [-D [bind_address:]port] [-E log_file] [-e escape_char]
           [-F configfile] [-I pkcs11] [-i identity_file]
           [-J [user@]host[:port]] [-L address] [-l login_name] [-m mac_spec]
           [-O ctl_cmd] [-o option] [-p port] [-Q query_option] [-R address]
           [-S ctl_path] [-W host:port] [-w local_tun[:remote_tun]]
           [user@]hostname [command]
```

* `ssh -v` 명령어가 위처럼 출력안되면 ssh 설치

```
$ ls -a ~/.ssh
.           ..          id_rsa      id_rsa.pub  known_hosts

$ pbcopy < ~/.ssh/id_rsa.pub
```
* 기존에 작성된 `id_rsa.pub `을 기반으로 ssh 인증
* `pbcopy` 명령어로 ssh값 복사

![](https://confluence.atlassian.com/bitbucket/files/304578655/755335794/1/1434638786771/add_ssh_key.png)


* Bitbucket 계정 설정에 ssh key 탭에ㅓ ssh등록

[참고링크](https://confluence.atlassian.com/bitbucket/set-up-ssh-for-git-728138079.html)
