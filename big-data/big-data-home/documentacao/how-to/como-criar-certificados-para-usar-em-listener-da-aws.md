
[Documentação](../../documentacao.md) > [How-to](../how-to.md)

# Como criar certificados para usar em listener da AWS

Utilize uma maquina com Linux, e siga o passo a passo abaixo:

1. openssl genrsa 2048 > my-aws-private.key
2. openssl req -new -x509 -nodes -sha1 -days 3650 -extensions v3\_ca -key my-aws-private.key > my-aws-public.crt
3. openssl pkcs12 -inkey my-aws-private.key -in my-aws-public.crt -export -out my-aws-public.p12
4. Upload o certificado (my-aws-public.crt) e o private key (my-aws-private.key) to AWS Certificate Manager
5. Em load balancer do Terraform, incluir mais um listener na 443/https para 80/http
