������������

grub and boot
�� Centos5��6�Ŀ�����������
�� grub
�� Centos7�Ŀ�����������
Centos5,6�Ŀ�����������

initrd / initramfs
һ��洢��/bootĿ¼�£���.imgΪ��β���ļ�����һ��С�͵ĸ�Ŀ¼ϵͳ��ӳ���ļ����������˸���ϵͳ�����ģ�飬Ϊ�˽���ں��ڼ������֮��û��ģ���޷����ʴ��̼���rootfs�����⡣
rc
��inittab���ļ��У�����ôһ�����si::sysinit:/etc/rc.d/rc.sysinit ����仰��ʾ�����õ�run level��ֵ��Ϊ���������rc����ű���
����������һ��rc����/etc/rc.d/rc��
����Ҫ�����Σ�
for i in /etc/rc$runlevel.d/K* ; do #ѭ���г�/etc/rc#.dĿ¼����K��ͷ���ļ�����ֵ��i��

        # Check if the subsystem is already up.
        subsys=${i#/etc/rc$runlevel.d/K??}           #���ַ�����Ƭ��ɾȥ��һ��/etc/rc$.d/K##�Ĳ��֡�
        [ -f /var/lock/subsys/$subsys -o -f /var/lock/subsys/$subsys.init ] || continue     #�����������ͬ���ļ��������ļ���.init�ķ���ű�����������ǰ��һ��ѭ����
        check_runlevel "$i" || continue  #�����������Ƿ���Ч��
        # Bring the subsystem down.
        [ -n "$UPSTART" ] && initctl emit --quiet stopping JOB=$subsys  
        $i stop                                                         #���ݵ�ǰ�������stop������
        [ -n "$UPSTART" ] && initctl emit --quiet stopped JOB=$subsys       
    done
    for i in /etc/rc$runlevel.d/S* ; do          #ѭ���г�/etc/rc#.dĿ¼����S��ͷ���ļ�����ֵ��i��
        # Check if the subsystem is already up.
        subsys=${i#/etc/rc$runlevel.d/S??}      subsys      #���ַ�����Ƭ��ɾȥ��һ��/etc/rc$.d/S##�Ĳ��֡�
        [ -f /var/lock/subsys/$subsys ] && continue         ������������ű���ͬ���ļ������ļ���������ǰ����ѭ����
        [ -f /var/lock/subsys/$subsys.init ] && continue        ������ڷ���ű�.init���ļ������ļ������ʹ׵�ǰ����ѭ����
        check_runlevel "$i" || continue     �����������Ƿ���Ч��
        # If we're in confirmation mode, get user confirmation
        if [ "$do_confirm" = "yes" ]; then      
            confirm $subsys
            rc=$?
            if [ "$rc" = "1" ]; then
                continue
            elif [ "$rc" = "2" ]; then
                do_confirm="no"
            fi
        fi
        update_boot_stage "$subsys"
        # Bring the subsystem up.   
        [ -n "$UPSTART" ] && initctl emit --quiet starting JOB=$subsys
        if [ "$subsys" = "halt" -o "$subsys" = "reboot" ]; then     ���ִ�е�halt����reboot�ű�����
            export LC_ALL=C     ȥ�����б��ػ�������
            exec $i start       ����start������rc#
        fi
        $i start        ����start����������ű���
        [ -n "$UPSTART" ] && initctl emit --quiet started JOB=$subsys
    done
���ܾ��Ǹ���ѡ���run levelȥ���ݶ�Ӧ��/etc/rc.d/rcһ�����֣�Ȼ��rc�ű��ͻ�ȥ������Ӧ��/etc/rc.d/rc#.dĿ¼�µ��ļ�����K[##�ر����ȼ�]��ͷ���ļ�����stop��������S[##�������ȼ�]��ͷ���ļ�����start���������򿪹رշ���
chkconfig������runlevel����������
chkconfig [��add | ��del] name ����һ���������/ɾ����ϵͳ����
chkconfig ��list name����ѯһ��������ÿһ��run level���Ƿ�Ϊ����������
chkconfig [��level levels] [��type type] [��no-redirect] name ������ָ����ĳһ��run level���ض������״̬��
grub
��Ҷ�֪���ڼ������bios�ж�ȡ����������˳��֮����ȥ��ȡMBR�Ĺ������ǳ�Ϊboot lorder�������������׶λ�ȥ��ȡMBR��ռ��446Byte��boot loader�еĳ��򣬶���Centos�ķ��а��л����϶���GRUB�������
446Byte�ж��ٴ��أ�446��Ӣ���ַ���Ҫ����ôС�Ŀռ��д��һ���������ǿ��ĳ�������ô�������أ�
grub��Ϊ�����֣�Ϊstage1��stage1.5��stage2��stage1�����boot lorder�У�stage1ָ������еĴ��1.5��2��Ŀ¼��һ��Ϊboot����Ȼ��ͨ��stage1.5�ļ��ļ����������/boot�����ݵõ�stage2��

grub���趨�ļ�
/boot/grub/grub.conf
��ЩbootĿ¼�л���menu.lst�ļ�����ϸ��Ŷ����ļ���ͨ�������ӵ���ʽ����grub.conf�ļ�ȥ�ġ�
��Ҫ���ԣ�
default=0 Ĭ��������ϵͳ��0�����һ����
timeout=5 ��ʱʱ�䡣
splashimage=(hd0,6)/boot/grub/splash.xpm.gz grub�ı������棬����Ϊָ����splash.xpm����ͼƬ~
hiddenmenu �Ƿ�����grub�������˵���
title Fedora Core (2.6.11-1.1369_FC4) �������������grub���濴���ĵ�һ��ҳ���ѡ��������~
root (hd0,6) ָ�����ϵͳ�ĸ�Ŀ¼
kernel /boot/vmlinuz-2.6.11-1.1369_FC4 ro root=LABEL=/ ָ�������Լ�rootfs
initrd /boot/initrd-2.6.11-1.1369_FC4.img ָ��initramfs
title WinXp ������ǵڶ�������XPŶ~
rootnoverify (hd0,0) ����root�����ǲ����԰�װ�÷�����
chainloader +1 GRUB��������ĵ�һ��������������¼��
grub�ֶ�ָ������
grub�������£�
grub> find /PATH�����Բ�ѯ�Ƿ���·����
grub> root (hd0,1)��ָ�����豸��
grub> kernel /vmlinux-version-relias.arch ro grub> root=ROOTFS_PATH��ָ���ںˡ�
grub> initrd /initrd-version-relias.arch.img��ָ��ramfs�ļ���
grub> boot����ʼ������

grub��װ���޸�
�������У�
grub-install ��root-directory=/boot /dev/sda ��ָ��bootĿ¼��stageҪд��Ĵ��̡�
grub�������У�
grub> chroot /mnt/sysimage ������������rootfs��
grub> root (hd0,1)��ָ��boot���ڵĸ�Ŀ¼��
grub> setup (hd0)����stage1д�뵽MBR�С�
Centos7�Ŀ�����������
��Centos5��6��ȣ�Centos7��ϵͳ�����ĵ�һ֧�����init���systemd��systemd������һ�������initϵͳ�����һ��ܹ���ϵͳ�ĸ���daemon�������õĶ���������빦�ܡ�
�� ֧��ƽ���������񣬲�������������������
�� ��������
�� ֧�ֿ�����״̬�ָ�
�� c groupԤ������Ӳ����Դ ��
֧��ƽ���������񣬲�������������������
�Ա�init�Ľű�������systemd�󵨵Ľ�������Ҫ�����Ķ���Ϊһ��һ����unit�����Զ�Ӧ�ĺ�׺�����֣����·�Ϊ��
�� ϵͳ����.service��
�� ���ص㣨.mount��
�� sockets��.sockets��
�� ϵͳ�豸��.device��
�� ����������.swap��
�� �ļ�·����.path��
�� ����Ŀ�꣨.target��
�� systemd��ʱ����.timer��
systemd��Ϊÿһ����Ҫ�����ķ�������һ���׽��֣���ʹ��ͬ��daemon֮�����ͨ�ţ���Ϊÿһ��daemon����һ��������,�ﵽһ��һ��İ�����������������
��������
init��ϵͳ����ʱ������������Ԥ��Ϊ�����ķ�����̣�����ϵͳ����ȴ����еķ�����������֮��Ż������û���¼��������������ϵͳ�����ٶȷǳ����������˷�ϵͳ��Դ��
systemd�Ͳ����˰����������ڿ���ʱ����������Ҫ�ķ���Ͳ����������õ�֮��Ż�ȥ���ã�Ȼ��ʹ����Ϻ�һ��ʱ�����ֻ�رա�
֧�ֿ�����״̬�ָ�
systemd�ܹ���ϵͳ��ǰ��״̬����Ϊ���մ�����������������Ҫʱ�ָ���ǰϵͳ״̬��
target
��systemd�У���ԭ��sysV��upstartһֱ������run level��Ϊ��target������ʽ������target��û��run level����Ϊ0-6�����Ǹ���ϸ�µķ��˷ǳ����target��
SysV�������� SystemdĿ�� ע��
0 runlevel0.target, poweroff.target �ж�ϵͳ��halt��
1, s, single runlevel1.target, rescue.target ���û�ģʽ
2, 4 runlevel2.target, runlevel4.target, multi-user.target �û��Զ�����������ͨ��ʶ��Ϊ����3��
3 runlevel3.target, multi-user.target ���û�����ͼ�ν��档�û�����ͨ���ն˻������¼��
5 runlevel5.target, graphical.target ���û���ͼ�ν��档�̳м���3�ķ��񣬲�����ͼ�ν������
6 runlevel6.target, reboot.target ����
emergency emergency.target ����ģʽ��Emergency shell��
systemd��������ͼ

systemctl����
systemctl [options]
show [NAME.unittype]����ʾָ������״̬��ǰ����ķ����б�
status [NAME.unittype]���鿴ָ��unit״̬��
list-units [��type TYPE] [��all]���鿴ָ��unit���͵�load���״̬������״̬��������
list-unit-files ��type TYPE [��all]:�鿴ָ��unit���͵�����״̬���Ƿ�Ϊ���������ȡ�

start [NAME.unittype]������ָ��unit��
stop [NAME.unittype]��ָֹͣ��unit��
restart [NAME.unittype]:��������unit��
reload [NAME.unittype]�����¼���unit��
reset [NAME.unittype]����ԭ����unit��
try-restart [NAME.unittype]��������������вŻ�����
enable [NAME.unittype]������Ϊ����������
disable [NAME.unittype]������Ϊ������������
isolate [NAME.target]���л���ָ��target��
get-default [NAME.target]����ѯ����Ĭ��target��
set-default [NAME.target]�����ÿ���Ĭ��target��
snapshot������һ�����գ�������ɺ����´�������ϵͳ���Զ��ظ�����״̬��
hibernate������ģʽ������Ϣд�뵽�ļ���,Ҳ����Ӳ����,�����жϵ綪ʧ���ݵ�����,���ָ�ʱ����,�����¿���һ����
sleepp��˯��ģʽ������Ϣ���浽�ڴ���,�����ܶϵ�,�ϵ�����ݶ�ʧ,�ָ���졣
hybrid-sleep�����˯��ģʽ��
reboot������
halt:�ػ�
systemd�ļ�
/etc/systemd/system.conf��systemd�趨����
/lib/systemd/system/NAME.UNIT��unit�ű��ļ�λ�á�
/etc/systemd/system/NAME.UNIT.wants�������ָ��targetҪ���������з���

��������һ��/etc/systemd/system/Ŀ¼���浽�״����ʲô�ļ���

[root@xiao ~]# ll /etc/systemd/system/
total 32
drwxr-xr-x. 2 root root 4096 Nov 21  2014 basic.target.wants
lrwxrwxrwx. 1 root root   37 Nov 21  2014 default.target -> /lib/systemd/system/multi-user.target   ע�����
drwxr-xr-x. 2 root root 4096 Nov 21  2014 default.target.wants
drwxr-xr-x. 2 root root 4096 Nov 21  2014 getty.target.wants
drwxr-xr-x. 2 root root 4096 Apr  8 14:29 multi-user.target.wants
drwxr-xr-x. 2 root root 4096 Nov 21  2014 printer.target.wants
drwxr-xr-x. 2 root root 4096 Nov 21  2014 sockets.target.wants
drwxr-xr-x. 2 root root 4096 Mar 22 17:48 sysinit.target.wants
drwxr-xr-x. 2 root root 4096 Nov 21  2014 system-update.target.wants
�����Ŀ¼���ǿ��Կ�������ʵϵͳ��ǰ�����е�target��ͨ�������ӵ���ʽ���ӵ�/lib/systemd/system/Ŀ¼�µ�target�ļ����������ַ�ʽ��
���ǾͿ���ͨ���ֶ����л�target��

[root@xiao system]#rm -f default.target
[root@xiao system]#ln -s /lib/systemd/system/graphical.target default.target