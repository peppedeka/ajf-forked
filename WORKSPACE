workspace(name = "ajf")

load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")

# Add NodeJS rules (explicitly used for sass bundle rules)
http_archive(
  name = "build_bazel_rules_nodejs",
  url = "https://github.com/bazelbuild/rules_nodejs/archive/0.16.3.zip",
  strip_prefix = "rules_nodejs-0.16.3",
)

# Add TypeScript rules
http_archive(
  name = "build_bazel_rules_typescript",
  url = "https://github.com/bazelbuild/rules_typescript/archive/0.22.0.zip",
  strip_prefix = "rules_typescript-0.22.0",
)

# Add Angular source and Bazel rules.
http_archive(
  name = "angular",
  url = "https://github.com/angular/angular/archive/7.1.3.zip",
  strip_prefix = "angular-7.1.3",
)

http_archive(
  name = "angular_material",
  url = "https://github.com/angular/material2/archive/bc8fc75bf8af82378077d7c2277e31a1dcd6aac9.zip",
  strip_prefix = "material2-bc8fc75bf8af82378077d7c2277e31a1dcd6aac9",
)

# Add RxJS as repository because those are needed in order to build Angular from source.
# Also we cannot refer to the RxJS version from the node modules because self-managed
# node modules are not guaranteed to be installed.
# TODO(gmagolan): remove this once rxjs ships with an named UMD bundle and we
# are no longer building it from source.
http_archive(
  name = "rxjs",
  url = "https://registry.yarnpkg.com/rxjs/-/rxjs-6.3.3.tgz",
  strip_prefix = "package/src",
  sha256 = "72b0b4e517f43358f554c125e40e39f67688cd2738a8998b4a266981ed32f403",
)

http_archive(
  name = "ngx_translate_core",
  url = "https://github.com/ngx-translate/core/archive/v11.0.0.zip",
  strip_prefix = "core-11.0.0/projects/ngx-translate/core/src",
  build_file="//tools/build_files/ngx-translate-core:BUILD.bazel.ngxtc",
  workspace_file="//tools/build_files/ngx-translate-core:WORKSPACE.ngxtc"
)

http_archive(
  name = "ionic_angular",
  url = "https://github.com/ionic-team/ionic/archive/v4.0.0-beta.19.zip",
  strip_prefix = "ionic-4.0.0-beta.19/angular/src",
  build_file="//tools/build_files/ionic:BUILD.bazel.ionic",
  workspace_file="//tools/build_files/ionic:WORKSPACE.ionic"
)

http_archive(
  name = "ionic_selectable",
  url = "https://github.com/gnucoop/ionic-selectable/archive/d32d1ad7d71ce452b18445a97ca849dcab019799.zip",
  strip_prefix = "ionic-selectable-d32d1ad7d71ce452b18445a97ca849dcab019799/src/app/components/ionic-selectable",
  build_file="//tools/build_files/ionic-selectable:BUILD.bazel.ionic-selectable",
  workspace_file="//tools/build_files/ionic-selectable:WORKSPACE.ionic-selectable"
)

http_archive(
  name = "ngx_dnd",
  url = "https://github.com/gnucoop/ng2-dnd/archive/56adf9265e01b2f468ce97abbcb4a1e48da0faf4.zip",
  strip_prefix = "ng2-dnd-56adf9265e01b2f468ce97abbcb4a1e48da0faf4/src/lib",
  build_file="//tools/build_files/ngx-dnd:BUILD.bazel.ngx-dnd",
  workspace_file="//tools/build_files/ngx-dnd:WORKSPACE.ngx-dnd"
)

http_archive(
  name = "ngx_color_picker",
  url = "https://github.com/zefoy/ngx-color-picker/archive/v7.2.0.zip",
  strip_prefix = "ngx-color-picker-7.2.0",
  build_file="//tools/build_files/ngx-color-picker:BUILD.bazel.ngx-color-picker",
  workspace_file="//tools/build_files/ngx-color-picker:WORKSPACE.ngx-color-picker"
)

# Add sass rules
http_archive(
  name = "io_bazel_rules_sass",
  url = "https://github.com/bazelbuild/rules_sass/archive/1.15.2.zip",
  strip_prefix = "rules_sass-1.15.2",
)

# Since we are explitly fetching @build_bazel_rules_typescript, we should explicitly ask for
# its transitive dependencies in case those haven't been fetched yet.
load("@build_bazel_rules_typescript//:package.bzl", "rules_typescript_dependencies")
rules_typescript_dependencies()

# Since we are explitly fetching @build_bazel_rules_nodejs, we should explicitly ask for
# its transitive dependencies in case those haven't been fetched yet.
load("@build_bazel_rules_nodejs//:package.bzl", "rules_nodejs_dependencies")
rules_nodejs_dependencies()

# Fetch transitive dependencies which are needed by the Angular build targets.
load("@angular//packages/bazel:package.bzl", "rules_angular_dependencies")
rules_angular_dependencies()

# Fetch transitive dependencies which are needed to use the Sass rules.
load("@io_bazel_rules_sass//:package.bzl", "rules_sass_dependencies")
rules_sass_dependencies()

load("@build_bazel_rules_nodejs//:defs.bzl", "check_bazel_version", "node_repositories",
    "yarn_install")

# The minimum bazel version to use with this repo is 0.18.0
check_bazel_version("0.18.0")

node_repositories(
  # For deterministic builds, specify explicit NodeJS and Yarn versions. Keep the Yarn version
  # in sync with the version of Travis.
  node_version = "10.10.0",
  yarn_version = "1.12.1",
)

# @npm is temporarily needed to build @rxjs from source since its ts_library
# targets will depend on an @npm workspace by default.
# TODO(gmagolan): remove this once rxjs ships with an named UMD bundle and we
# are no longer building it from source.
yarn_install(
  name = "npm",
  package_json = "//tools:npm/package.json",
  yarn_lock = "//tools:npm/yarn.lock",
)

# Setup TypeScript Bazel workspace
load("@build_bazel_rules_typescript//:defs.bzl", "ts_setup_workspace")
ts_setup_workspace()

# Setup the Sass rule repositories.
load("@io_bazel_rules_sass//:defs.bzl", "sass_repositories")
sass_repositories()

# Setup Angular workspace for building (Bazel managed node modules)
load("@angular//:index.bzl", "ng_setup_workspace")
ng_setup_workspace()

load("@angular_material//:index.bzl", "angular_material_setup_workspace")
angular_material_setup_workspace()

load("@ajf//:index.bzl", "ajf_setup_workspace")
ajf_setup_workspace()

# Setup Go toolchain (required for Bazel web testing rules)
load("@io_bazel_rules_go//go:def.bzl", "go_rules_dependencies", "go_register_toolchains")
go_rules_dependencies()
go_register_toolchains()

# Setup web testing. We need to setup a browser because the web testing rules for TypeScript need
# a reference to a registered browser (ideally that's a hermetic version of a browser)
load("@io_bazel_rules_webtesting//web:repositories.bzl", "browser_repositories",
    "web_test_repositories")

web_test_repositories()
browser_repositories(
  chromium = True,
)