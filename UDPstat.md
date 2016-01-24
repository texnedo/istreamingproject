Программа для получения статистики передачи UDP пакетов.

Links:

1. http://stackoverflow.com/questions/2090850/specifying-udp-receive-buffer-size-at-runtime-in-linux

2. http://git.kernel.org/?p=linux/kernel/git/torvalds/linux-2.6.git;a=commitdiff;h=7b4f4b5ebceab67ce440a61081a69f0265e17c2a

3. http://wiki.wireshark.org/Tools#Traffic_generators


root@westcoast:/home/kvant# tcpreplay -v -l 10000 -t

root@westcoast:/home/kvant# tcpreplay -v -l 10000 -t -i wlan0 Downloads/udp\_test.pcap
> - убивает сеть на интерфейсе напрочь

root@westcoast:/home/kvant# tcpreplay -v -l 10000 -M 0.007 -i wlan0 Downloads/udp\_test.pcap
-увеличивает пинг, 26% потерь


CREATE TABLE `recv_log` (
> `Id` bigint(20) NOT NULL,
> `Sec` bigint(20) NOT NULL,
> `Usec` bigint(20) NOT NULL,
> PRIMARY KEY (`Id`)
) ENGINE=MyISAM DEFAULT CHARSET=latin1

CREATE TABLE `send_log` (
> `Id` bigint(20) NOT NULL,
> `Sec` bigint(20) NOT NULL,
> `Usec` bigint(20) NOT NULL,
> PRIMARY KEY (`Id`)
) ENGINE=MyISAM DEFAULT CHARSET=latin1

CREATE DEFINER=`root`@`localhost` PROCEDURE `Compute_packet_loss`()
BEGIN
SELECT (SELECT COUNT(**) FROM `mysql`.`send_log` send
WHERE send.Id NOT IN(SELECT Id FROM `mysql`.`recv_log`))/(SELECT COUNT(**)
FROM `mysql`.`send_log`) as 'LOSS';
END

CREATE DEFINER=`root`@`localhost` PROCEDURE `Count_burst_errors`()
BEGIN

DECLARE done INT DEFAULT 0;
DECLARE pa, pb, pc, pa1, pb1, pc1 INT;
DECLARE group\_start INT;
DECLARE group\_len INT;
DECLARE avg\_group\_len INT;
DECLARE a, b, c, a1, b1, c1 INT;
DECLARE burst\_ctr INT DEFAULT 0;
DECLARE error\_log CURSOR FOR (SELECT **FROM `mysql`.`send_log` send
LEFT JOIN `mysql`.`recv_log` recv ON send.Id = recv.Id
WHERE recv.Id IS NULL);
DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;**


OPEN error\_log;
SET group\_len = 1;
SET avg\_group\_len =0;
FETCH error\_log INTO pa, pb, pc, pa1, pb1, pc1; -- предыдущие значения
read\_loop: LOOP
> FETCH error\_log INTO a, b, c, a1, b1, c1;
> SET group\_len = group\_len + 1;
> IF (a <> pa + 1) AND ( group\_len > 1) THEN -- закончилась группа
> > SET burst\_ctr = burst\_ctr + 1;
> > SET avg\_group\_len = avg\_group\_len + group\_len;
> > SET group\_len = 0;

> END IF;
> SET pa = a;
> IF done THEN
> > LEAVE read\_loop;

> END IF;

END LOOP;
CLOSE error\_log;

SELECT burst\_ctr as "burst packet counter";
SELECT avg\_group\_len/burst\_ctr as "average group length";

END


http://stackoverflow.com/questions/53491/how-do-i-enable-external-access-to-mysql-server


Проблема с синхронизацией:
http://www.walkernews.net/2007/03/08/configure-linux-ntp-time-synchronization/
http://stackoverflow.com/questions/97853/whats-the-best-way-to-synchronize-times-to-millisecond-accuracy-and-precision-be
http://perdues.com/doc/ntp.html


Исследование поведения реал таймового траффика для передачи видиео и аудиопотоков в условиях различной загрузки сети.

http://delicast.com/tv/Portugal/news/Porto_Canal - пример стримингового видео. По всей видимости все построено на TCP.

Информация про UDP буффер и его значение:
http://www.29west.com/docs/THPM/udp-buffer-sizing.html
http://www.29west.com/docs/THPM/udp-buffering-background.html