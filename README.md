# basic lambda written in Go

# background:  Go and AWS Lambda
- Go is implemented differently than other managed runtimes. 
- Because Go compiles to native code, Lambda treats Go as a custom runtime. 
- We recommend that you use the **provided.al2023** or **provided.al2** runtime to deploy Go functions to Lambda.

> The provided.al2023 and provided.al2 runtimes offer several advantages over go1.x, including support for the arm64 architecture (AWS Graviton2 processors), smaller binaries, and slightly faster invoke times.

## links

- https://docs.aws.amazon.com/lambda/latest/dg/lambda-golang.html
- aws-lambda-go https://github.com/aws/aws-lambda-go