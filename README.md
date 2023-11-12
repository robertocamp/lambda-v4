# How to Write AWS Lambda functions in Go

# Background:  Go and AWS Lambda
- Go is implemented differently than other managed runtimes. 
- Because Go compiles to native code, Lambda treats Go as a *custom runtime*. 
- recommended providers     git push --set-upstream origin initial-commit:   **provided.al2023** or **provided.al2**

> The provided.al2023 and provided.al2 runtimes offer several advantages over go1.x, including support for the arm64 architecture (AWS Graviton2 processors), smaller binaries, and slightly faster invoke times.

## example Project1:

https://github.com/aws-samples/sessions-with-aws-sam/tree/master/go-al2


## links

- [AWS SDK](https://github.com/aws/aws-sdk-go)
- [implementation of the Lambda programming model for Go](https://github.com/aws/aws-lambda-go/tree/main/lambda)
  + *This package is used by AWS Lambda to invoke your handler*
- [context object helpers](https://github.com/aws/aws-lambda-go/tree/main/lambdacontext)
- [event source integration](https://github.com/aws/aws-lambda-go/tree/main/events)

https://docs.aws.amazon.com/lambda/latest/dg/lambda-golang.html
- aws-lambda-go https://github.com/aws/aws-lambda-go