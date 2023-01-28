```bash
declare -a arr_vpn=()


clearRules(){# очищаем правила, запрещаем соединения
    iptables -F
        iptables -X
        iptables -t nat -F
        iptables -t nat -X
        iptables -t mangle -F
        iptables -t mangle -X
        iptables -t raw -F
        iptables -t raw -X
        iptables -t security -F
        iptables -t security -X
        
        iptables -P INPUT ACCEPT
        iptables -P FORWARD ACCEPT        
# (ВРЕМЕННО ОТКЛЮЧЕН DROP)
# РАЗРЕШЕН ACCEPT
#        iptables -P OUTPUT DROP
#        iptables -P OUTPUT ACCEPT
}
append_vpn_serviceList(){
     arr_vpn=(${arr_vpn[@]} ''$1) #SEE FIX
}
iptables_standard_mark_vpn_service(){
    for t in ${arr_vpn[@]}; do
        iptables -A OUTPUT -p udp -d $t -j ACCEPT
        iptables -A INPUT -p udp -s $t -j ACCEPT
        iptables -A OUTPUT -p tcp -d $t -j ACCEPT
        iptables -A INPUT -p tcp -s $t -j ACCEPT
    done        
}
save(){
    iptables-save -f /etc/iptables/iptables.rules
}
#разрешаем соедиения внутри пк и в vpn
iptables -A OUTPUT -o lo -j ACCEPT
iptables -A OUTPUT -o tun+ -j ACCEPT

#добавляем ip vpn сервиса
#append_vpn_serviceList ip

#логирование
iptables -A OUTPUT  -j LOG --log-level 7 --log-prefix "OUT:DROP " --log-uid
#сбрасываем соединения (ВРЕМЕННО ОТКЛЮЧЕН)
#iptables -A OUTPUT -j REJECT
save
```
