# CI runner routing and artifact retention

Linux jobs use disposable public AWS CodeBuild runners. Native macOS jobs retain their platform. Artifacts use S3 run/attempt/commit paths with 30-day retention, while dependency caches have 14-day retention. Cross-workflow PR metadata is treated only as data and must match the trusted GitHub workflow event before any privileged PR operation. Missing or mismatching evidence fails closed.
