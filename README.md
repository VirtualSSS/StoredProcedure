# StoredProcedure
# sql server 将查询出来的多行记录拼接成一行 
select
(select max(x1.skuDescr1) from DOC_ORDER_DETAILS x 
inner join BAS_SKU x1 on x1.organizationId=x.organizationId and x1.customerId=x.customerId and x1.sku=x.sku and x1.activeFlag='Y'
where x.organizationId=t.organizationId 
and x.warehouseId=t.warehouseId
and x.orderNo=t.orderNo
and charindex('底版',x1.reservedField13)=0
) +' '+ 
case when charindex('底版',t1.reservedField13)>0 then t1.skuDescr1 else '' end + ':'+
(stuff((select '+'+a1.reservedField04 from DOC_ORDER_DETAILS a 
inner join BAS_SKU a1 on a1.organizationId=a.organizationId and a1.customerId=a.customerId and a1.sku=a.sku
where a.organizationId=t.organizationId
and a.orderNo=t.orderNo
and charindex('底版',a1.reservedField13)=0
for xml path('')),1,1,''))
from DOC_ORDER_DETAILS t 
inner join BAS_SKU t1 on t1.organizationId=t.organizationId and t1.customerId=t.customerId and t1.sku=t.sku
where t.organizationId='SLZS'
and t.orderNo='SB1C23041800012'
and charindex('底版',t1.reservedField13)>0
group by t.organizationId,t.warehouseId,t1.skuDescr1,t.orderNo,t1.reservedField13
