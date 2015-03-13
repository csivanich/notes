SSH Defense Strategies
==========
Chris Sivanich ©2015

---------

#### Some Content From:

- [Flexion.org](https://flexion.org/posts/2012-11-ssh-brute-force-defence.html)
- [Fail2ban on Arch Wiki](https://wiki.archlinux.org/index.php/Fail2ban)
- [VigilCode.com](http://blog.vigilcode.com/2011/05/ufw-with-fail2ban-quick-secure-setup-part-ii/)

### Fail2ban

#### Use systemd backend

- /etc/fail2ban/jail.local

        [DEFAULT]
        ...
        backend = systemd

#### Setup SSH Jail

- /etc/fail2ban/jail.d/sshd.conf

        # fail2ban SSH
        # block ssh after 3 unsuccessful login attempts for 10 minutes
        [sshd]
        enabled  = true
        # action   = iptables[chain=INPUT, protocol=tcp, port=22, name=sshd] # Stock action
        action = ufw-ssh # Custom action defined in next section
        maxRetry = 3
        findtime = 600  # Time to group failed attempts (10 minutes)
        bantime  = 600  # Time to ban (10 minutes)
        port     = 22

#### Adding Action

 - /etc/fail2ban/action.d/ufw-ssh.conf

        [Definition]
        actionstart =
        actionstop =
        actioncheck =
        actionban = ufw insert 1 deny from <ip> to any app OpenSSH
        actionunban = ufw delete deny from <ip> to any app OpenSSH

#### Helpful Commands

        fail2ban-client start sshd
        fail2ban-client stop sshd
        fail2ban-client reload sshd
        fail2ban-client status sshd
        fail2ban-client set sshd unbanip 172.16.0.4
