stage name: "test"
parallel(
  failFast: false,
  "windows": {
    wrappedNode(label: "windows") {
      deleteDir()
      sh "git config --global core.autocrlf input"
      checkout scm
      withEnv(["GOPATH=/tmp/${env.BUILD_TAG}", "PATH=/tmp/${env.BUILD_TAG}/bin:${env.PATH}"]) {
        withTool("golang@1.6") {
          try {
            sh """
            mkdir -p "\$GOPATH/bin" "\$GOPATH/src/github.com/docker"
            ln -nfs \$(pwd) "\$GOPATH/src/github.com/docker/engine-api"
            cd "\$GOPATH/src/github.com/docker/engine-api"
            go get -t ./...
            go get github.com/golang/lint/golint
            go vet ./...
            golint ./...
            gofmt -s -l .
            go test -race -cover -v -tags=test ./...
            """
          } finally {
            sh "rm -rf \"\$GOPATH\" ||:"
          }
        }
      }
    }
  },
  "linux": {
    wrappedNode(label: "linux && x86_64") {
      deleteDir()
      checkout scm
      sh """
      docker run \\
        --rm \\
        -i \\
        -v "\$(pwd):/go/src/github.com/docker/engine-api" \\
        -w /go/src/github.com/docker/engine-api \\
        golang:1.6.2 \\
        make deps validate test
      """
    }
  }
)
