#$Id: sysinfo.pl,v 1.3 2001/05/23 13:16:52 where Exp $

#!/usr/bin/perl -w

# -----------------------------------------------------------------
# whoo's hacked up sysinfo for x-chat v.0.3.0 (archived version)
# Originally avaliable at http://www.owenmeany.com/linux.html
# Author: whoo
# Support: http://www.village-idiot.org
#
# If you use pico to edit use the -w switch ( pico -w )   <------READ!!
#
# Check the variables, if your not using lm_sensors, remove ALL the refernces to it
# All of the variables are explained...  ...read the perl Luke..
# PS: I could NOT have done any of this without the help of a few people,
# Xwild, for making me fix the dual processor bugs 
# sys_rage, for being my conscious, and a most tolerant teacher, 
# considering how damn bitchy I can be 
# and elfing, for throwing perl books at my feet
# Come visit us all on irc server us.undernet.org in #/dev/null :)
# Bargraph for memory via mIRC style is courtesy of 
# Sebastian Wenzler <wenzleratsnospamsays.windows-sucks.com>
# Fix for traffic/in traffic/out not just showing in Gbytes is 
# courtesy of caetin <caetin@u.washington.edu>
# usage : /sys /temp or /net
# -----------------------------------------------------------------
# YOU MUST ASSIGN THE VARIABLE BELOW SINCE IM TOO LAZY
# TO FIGURE OUT WHAT YOU WANT FOR YOUR NETDEVICE

$DEV = eth0;   ## <-------------------------------CHANGE THAT


IRC::register("Sysinfo", "0.2.4", "", "");
IRC::print "Loading whoo's hacked sysinfo script";
IRC::add_command_handler("sys", "display_sys_info");
IRC::add_command_handler("net", "net");
IRC::add_command_handler("temp", "temp");
sub net
{
    #--PACKETS IN--#
    $PACKIN = `cat /proc/net/dev | grep $DEV | awk -F: '/:/ {print \$2}' | awk '{printf \$1}'`;
    if($PACKIN < 1024**3) { $PACKIN = sprintf("%.02f",$PACKIN / 1024**2)."M"; } else { $PACKIN = sprintf("%.02f", $PACKIN / 1024**3)."G"; }

    #--PACKETS OUT--#
    $PACKOUT = `cat /proc/net/dev | grep $DEV | awk -F: '/:/ {print \$2}' | awk '{print \$9}'`;
    if($PACKOUT < 1024**3) { $PACKOUT = sprintf("%.02f",$PACKOUT / 1024**2)."M"; } else { $PACKOUT = sprintf("%.02f", $PACKOUT / 1024**3)."G"; }

  #--SPEW IT INTO THE CHANNEL SHALL WE--#
IRC::command("Stats:  Leeched: $PACKIN Sent: $PACKOUT");
return 1;
}   

sub temp
{
    #--LM_SENSORS #1--#
    $SENSOR1 = `sensors -f | grep SYS | awk '{print \$1, \$2, \$3}'`;
    chop ($SENSOR1);

    #--LM_SENSORS #2--#
    $SENSOR2 = `sensors -f | grep 'CPU Temp' | awk '{print \$1, \$2, \$3}'`;
    chop ($SENSOR2);

    #--SPEW IT INTO THE CHANNEL SHALL WE--#
IRC::command("$SENSOR1 $SENSOR2");
return 1;
}

sub display_sys_info
{  
    
    #--UNAME--#
    $UNAME = `uname -sr`;
    chop ($UNAME);
    
    #--MODEL--#
    $MODEL = `cat /proc/cpuinfo | grep '^model name' | head -1 | sed -e 's/^.*: //'`;
    
    #--HOW MANY CPUS--#
    $NUM=`cat /proc/cpuinfo | grep "model name" | wc -l | sed 's/ *//'`;
    chop ($NUM);
    
    #--PROCESSOR SPEED--#
    $CPU = `cat /proc/cpuinfo | grep 'cpu MHz' | head -1 | sed -e 's/^.*: //'`;
    chop ($CPU);
    
    #--PROCS RUNNING--#
    $PROCS = `ps ax | wc -l | awk '{print \$1 - 5}'`;
    chop ($PROCS);
    
    #--UPTIME--#
#    $UPTIME = `up | awk '{ print }'`;
     @UPTIME = split " ", `up -n`;
     $UPTIME = $UPTIME[1] ? "$UPTIME[1]y " : "";
     $UPTIME .= $UPTIME[2] ? "$UPTIME[2]w " : "";
     $UPTIME .= $UPTIME[3] ? "$UPTIME[3]d " : "";
     $UPTIME .= $UPTIME[4] ? "$UPTIME[4]h " : "";
     $UPTIME .= $UPTIME[5] ? "$UPTIME[5]m " : "";    
     chop ($UPTIME);
    
    #--TOTAL MEMORY--#
    $MEMTOTAL = `free | grep Mem | awk '{printf (\"%dMb\", \$2/1000 )}'`;
    chop ($MEMTOTAL);
    
    #--PERCENTAGE OF MEMORY FREE--#
    $MEMPERCENT = `free | grep Mem | awk '{print (( \$3 -(\$6 + \$7) )/\$2)*100}'`;
    chop ($MEMPERCENT);
    
    #--MEMORY FREE--#
    $MEMFREE = `free | grep Mem | awk '{printf (\"%.0fg\", ( \$3 -(\$6 + \$7) )/1000)}'`;
    chop ($MEMFREE);
    
   
   #--PERCENTAGE OF MEMORY FREE--#
    my $MEMPERCENT = sprintf("%.1f",(100/${MEMTOTAL}*${MEMFREE}));
    
    #--BARGRAPH OF MEMORY FREE--#
    my $FREEBAR = int(${MEMPERCENT}/10);
    my $MEMBAR; 
    my $x;
    
    if ( $color ) {
        $MEMBAR = "%B[%C3";
        # $MEMBAR = "%C14[%C1,3";
    } else {
        $MEMBAR  = "\002[";
    }
        
    for ($x = 0;$x < 10; $x++)
    {
        if ( $x == $FREEBAR ) {
            if ( $color ) {
                $MEMBAR .= "%C4";
                #$MEMBAR .= "%C1,4";
            } else {
                $MEMBAR .= "\002"; 
            }
        }
        $MEMBAR .= "\|";
    }
    
    if ( $color ) {
        $MEMBAR .= "%O%B]%O";
        # $MEMBAR .= "%C%C14]%C";
    } else {
        $MEMBAR .= "\002]\002";  
    }

    #--SCREEN RESOLUTION--#
    $RES = `xdpyinfo | grep dimensions | awk '{print \$2}'`;
    chop ($RES);
    
    #--DISKSPACE--#
    $HDD = `df | awk '{ sum+=\$2/1024^2 }; END { printf (\"%dGb\", sum )}'`;
    chop ($HDD);
    
    #--DISKSPACE FREE--#
    $HDDFREE = `df | awk '{ sum+=\$4/1024^2 }; END { printf (\"%dGb\", sum )}'`;
    chop ($HDDFREE);
    
    #--BOGOMIPS--#
    $MIPS = `cat /proc/cpuinfo | grep '^bogomips' | awk '{ sum+=\$3 }; END { print sum }'`;
    chop ($MIPS);
    
    #--LM_SENSORS #1--#
    $SENSOR1 = `sensors -f | grep SYS | awk '{print \$1, \$2, \$3}'`;
    chop ($SENSOR1);
    
    #--LM_SENSORS #2--#
    $SENSOR2 = `sensors -f | grep 'CPU Temp' | awk '{print \$1, \$2, \$3}'`;
    chop ($SENSOR2);
    
    #--PACKETS IN--# 
    $PACKIN = `cat /proc/net/dev | grep $DEV | awk -F: '/:/ {print \$2}' | awk '{printf \$1}'`;
    if($PACKIN < 1024**3) { $PACKIN = sprintf("%.02f",$PACKIN / 1024**2)."M"; } else { $PACKIN = sprintf("%.02f", $PACKIN / 1024**3)."G"; }
 
   #--PACKETS OUT--# 
    $PACKOUT = `cat /proc/net/dev | grep $DEV | awk -F: '/:/ {print \$2}' | awk '{print \$9}'`; 
    if($PACKOUT < 1024**3) { $PACKOUT = sprintf("%.02f",$PACKOUT / 1024**2)."M"; } else { $PACKOUT = sprintf("%.02f", $PACKOUT / 1024**3)."G"; }

    #--SUPPORT FOR DUAL PROCS--#
    unless($NUM eq 1 ) { $MODEL="Dual $MODEL"; }
    chop ($MODEL);

    #--SPEW IT INTO THE CHANNEL SHALL WE--#
IRC::command("SysInfo: $UNAME | $MODEL $CPU MHz | Mem: $MEMFREE/$MEMTOTAL $MEMBAR | Diskspace: $HDD Free: $HDDFREE | Bogomips: $MIPS | Screen Res: $RES | Procs: $PROCS | $SENSOR1 $SENSOR2 | Up: $UPTIME | $DEV: In: $PACKIN Out: $PACKOUT");
return 1;
}
