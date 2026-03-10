
[Documentação](../../documentacao.md) > [Azure](../azure.md)

# Componentes por Regiao

A relação atualizada de serviços do Azure por região está disponível em <https://azure.microsoft.com/pt-br/regions/services/>,  a partir dessa URL foi gerada a planilha "[Azure Products available by region.xlsx](../../../attachments/146768053.xlsx)" , destacando em amarelo quais são de interesse de BI.

As regiões "**EAST US**" e "**EAST US 2**" dispõe da maioria dos componentes, além de apresentarem um tempo médio de latência baixo em relação as demais regiões, sendo fortes candidatos para os próximos recursos criados no Azure, porém, a indicação de "Available" não garante que o serviço esteja disponível para essas regiões no momento de sua criação.

Segundo nosso contato na Microsoft, além da disponibilidade dos serviços, outros fatores precisam entrar na equação para escolher um data center:

1. Característica da sua aplicação – online ou batch, e o tempo de resposta esperado.
2. Possibilidade de se usar um cache (ex: CDN ou Akamai) para dados estáticos.
3. Custo dos componentes– o valor dos serviços do Brasil é maior que nos EUA.
4. Custo de tráfego intra regiões – por exemplo, se você tiver um ambiente nos EUA se comunicando com um no Brasil existirá custo de tráfego de outbound (<https://azure.microsoft.com/en-us/pricing/details/bandwidth/>).
5. Possibilidade de se utilizar um link dedicado (Express Route) entre o seu ambiente e o data center Azure (reduz a latência, oferece QoS, aumenta a segurança, etc).

**Ferramentas para teste de latência disponíveis em:**  
<http://www.azurespeed.com/> e <https://azurespeedtest.azurewebsites.net/>
