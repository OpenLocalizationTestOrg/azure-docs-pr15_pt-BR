Recurso|Limite padrão|Limite máximo
---|---|---
[Funções da Web/trabalhador por implantação](../articles/cloud-services/cloud-services-choose-me.md) <sup>1</sup>|25|25
[Pontos de extremidade de entrada de instância](http://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) por implantação|25|25
[Pontos de extremidade de entrada](http://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) por implantação|25|25
[Pontos de extremidade internos](http://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) por implantação|25|25

<sup>1</sup> Cada serviço de nuvem com funções da Web/trabalhador pode ter duas implantações, um para produção e outro para preparação. Observe também que esse limite se refere ao número de funções distintas (configuração) e não o número de instâncias por função (dimensionamento).
