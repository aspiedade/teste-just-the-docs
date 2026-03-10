
[Documentação](../../../../documentacao.md) > [AWS](../../../aws.md) > [Data Lake](../../data-lake.md) > [Metabase](../metabase.md)

# Historico de execucao de consultas

Com esse script é possivel verificar a quantidade de consultas realizadas por usuário, inclusive filtrando qual banco foi utilizado.

Neste exemplo, foi filtrado o database\_id = 7 que é referente ao Redshift.

```sql
SELECT  `s`.`started_at` AS `Dia`,
         CONCAT(`s`.`core_user__first_name`," ",`s`.`core_user__last_name`) AS `Nome`,
         `s`.`count` AS `Quantidade` 
         FROM (
			SELECT
			    date(`q`.`started_at`) AS `started_at`,
			    `c`.`first_name` AS `core_user__first_name`, 
				`c`.`last_name` AS `core_user__last_name`,
				count(*) AS `count` 
				FROM `query_execution` `q`
				LEFT JOIN `core_user` `c` ON `q`.`executor_id` = `c`.`id`
			WHERE (`q`.`database_id` = 7 )
			GROUP BY `c`.`first_name`, `c`.`last_name`, date(`q`.`started_at`)
			ORDER BY `c`.`first_name` ASC, `c`.`last_name` ASC, date(`q`.`started_at`) ASC
			) `s` 
	WHERE (`s`.`started_at` >= date(date_add(now(6), INTERVAL -7 day)) AND `s`.`started_at` < date(date_add(now(6), INTERVAL 1 day))) 
	ORDER BY Dia desc
```
