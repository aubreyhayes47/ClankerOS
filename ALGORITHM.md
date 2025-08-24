; ============================================
;  CLANKEROS END-TO-END MONOREPO ALGORITHM
;  "From docs → code → tests → CI → release"
; ============================================

        ORG     0x0000

; ---- BOOT: REPO LANDING + POLICIES --------------------------
BOOT:   CALL    PREP_REPO_ROOT               ; README, LICENSE, CoC, etc.
        CALL    WRITE_PROJECT_CHARTERS       ; TSC, SIG-Arch, RFC/ADR stages
        CALL    SET_CODEOWNERS_AND_OWNERSHIP ; Cross-workstream sign-offs
        CALL    ESTABLISH_DATA_POLICY        ; LFS + artifacts bucket policies
        JMP     ENV_SETUP

; (Rationale: v0.1 requires formal governance + cross-cutting interfaces + LFS/data policy.) 
; 

; ---- DEV ENV: NIX + BAZEL -------------------------------
ENV_SETUP:
        CALL    PIN_NIX_FLAKES               ; flake.nix + flake.lock lockstep
        CALL    CONFIG_BAZEL_RULES           ; Starlark rules + //... layout
        CALL    MAKE_SHIMS                   ; ./mk test / build / fmt wrappers
        CALL    SPARSE_WORKFLOWS             ; git sparse-checkout profiles
        JMP     DOCS_SCaffold

; (Bazel+Nix hermeticity; sparse checkout to reduce cognitive load.) 
; 

; ---- DOCUMENTATION SCAFFOLD --------------------------------
DOCS_SCAFFOLD:
        CALL    WRITE_READMES                ; root + per-workstream
        CALL    WRITE_ARCH_OVERVIEWS         ; SoC top, NoC, HAL, memory map
        CALL    RFC_TEMPLATES_ADR            ; multi-stage RFC/ADR templates
        CALL    ONBOARDING_TRACKS            ; software, hardware, CI-admin guides
        JMP     CI_FOUNDATION

; (Foundational doc set + interface specs + onboarding.) 
; 

; ---- CI/CD FOUNDATION (SOFTWARE + DOCS) --------------------
CI_FOUNDATION:
        CALL    CI_PATH_FILTERS              ; paths-filter so only impacted targets run
        CALL    CI_REMOTE_CACHE              ; Bazel remote cache + RBE endpoints
        CALL    CI_TIERS_SOFT                ; lint/format → unit → integration
        CALL    DOCS_CHECKS                  ; linkcheck, mdlint, ADR lints
        JMP     HW_CI_REARCH

; (Scalable CI needs path filters + remote cache; fast feedback loops.) 
; 

; ---- HARDWARE CI RE-ARCHITECTURE ---------------------------
HW_CI_REARCH:
        CALL    PROVISION_SELF_HOSTED        ; 32+ cores, 128GB RAM, NVMe runners
        CALL    HW_CI_TIERS                  ; PR: lint+smoke; main: regressions; nightly: synth/PnR/STA
        CALL    EDA_ENVS_NIX                 ; Yosys/OpenROAD/Verilator via Nix toolchains
        CALL    HW_ARTIFACT_RETENTION        ; store waves, checkpoints in artifacts storage (not git)
        JMP     SECURITY_SUPPLY

; (Replace naive hw-ci.yml with staged pipeline on self-hosted runners + artifacts policy.) 
; 

; ---- SECURITY & SUPPLY CHAIN -------------------------------
SECURITY_SUPPLY:
        CALL    SBOM_SOFTWARE                ; Anchore or equivalent for SW
        CALL    HBOM_HARDWARE                ; IP metadata, PDK/EDA versions, commit hashes
        CALL    SIGNING_PROVENANCE           ; attestations for releases
        JMP     GOVERNANCE_ENFORCE

; (Extend SBOM to HBOM for RTL/IP/PDKs/EDA provenance.) 
; 

; ---- GOVERNANCE ENFORCEMENT LOOPS --------------------------
GOVERNANCE_ENFORCE:
        CALL    REQUIRE_TSC_SIGARCH          ; CODEOWNERS for NoC/HAL/MEMMAP
        CALL    RFC_STAGES_ENFORCED          ; "proposal→implementable→implemented" gates
        CALL    MAINTAINERS_ROLES            ; review SLAs, conflict escalation
        JMP     WORKSTREAM_INIT

; (Charters, SIG-Arch authority, cross-workstream reviews baked in.) 
; 

; ---- WORKSTREAM BRING-UP (W1..W11) -------------------------
WORKSTREAM_INIT:
        MOV     R0, #1                       ; W = 1
WS_LOOP:
        CALL    WS_SCAFFOLD(R0)              ; README, ARCHITECTURE, INTERFACE, VERIF_PLAN, ROADMAP
        CALL    WS_MIN_TESTS(R0)             ; at least smoke tests wired to CI
        ADD     R0, R0, #1
        CMP     R0, #11
        BLE     WS_LOOP
        JMP     DEV_LOOP

; (Each workstream ships docs + interfaces + tests early; W11 integrates system.) 
; 

; ---- DAILY DEV LOOP ----------------------------------------
DEV_LOOP:
        CALL    OPEN_ISSUE_OR_RFC            ; every change mapped to Issue/RFC/ADR
        CALL    CREATE_BRANCH                ; conventional branch naming
        CALL    CODE_AND_TEST                ; bazel test //... in sparse scope
        CALL    RUN_PRECOMMIT                ; fmt/lint/hdl-lint
        CALL    PUSH_PR                      ; PR template, link issue
        CALL    AWAIT_REQUIRED_REVIEWS       ; CODEOWNERS auto-gates critical interfaces
        CALL    CI_RESULT_CHECK              ; if red, GOTO FIX_AND_RETRY
        JMP     MERGE_STRATEGY

FIX_AND_RETRY:
        CALL    TUNE_TESTS                   ; reduce flakiness/timeout; bisect failures
        CALL    UPDATE_DOCS                  ; keep specs in lockstep
        CALL    RERUN_CI
        JMP     DEV_LOOP

; ---- MERGE + POST-MERGE JOBS -------------------------------
MERGE_STRATEGY:
        CALL    SQUASH_OR_MERGE              ; maintain clean history; enforce DCO
        CALL    MAIN_BRANCH_JOBS             ; extended regressions; coverage upload
        CALL    NIGHTLY_JOBS                 ; synth/PnR/STA; long sims; docs site publish
        JMP     RELEASE_FLOW

; ---- RELEASE FLOW ------------------------------------------
RELEASE_FLOW:
        CALL    VERSION_BUMP                 ; SemVer + HW varianting
        CALL    AGGREGATE_SBOM_HBOM          ; collect SW SBOM + HW HBOM into artifacts
        CALL    TAG_AND_SIGN                 ; cryptographic signatures + provenance
        CALL    GITHUB_RELEASE               ; notes from CHANGELOG, attach artifacts
        JMP     COMMUNITY

; ---- COMMUNITY & SUPPORT -----------------------------------
COMMUNITY:
        CALL    DISCUSSIONS_TRIAGE           ; support flows; issue triage cadence
        CALL    GOOD_FIRST_ISSUES            ; maintain contributor runway
        CALL    METRICS_AND_HEALTH           ; CI duration, flake rate, coverage dashboards
        JMP     IMPROVEMENT_LOOP

; ---- CONTINUOUS IMPROVEMENT LOOP ---------------------------
IMPROVEMENT_LOOP:
        CALL    RETROSPECTIVES               ; monthly: CI pain, flake heatmap, infra needs
        CALL    ROADMAP_UPDATE               ; adjust milestones (v0.1→v0.2 FPGA→v0.4 SKY130)
        CALL    DOCS_DRIFT_CHECK             ; detect stale specs vs code
        JMP     DEV_LOOP                     ; iterate forever

; ============================================================
; ----------- SUBROUTINE DEFINITIONS (ABBREV.) --------------
; ============================================================

PREP_REPO_ROOT:
        WRITE   "README.md","CONTRIBUTING.md","CODE_OF_CONDUCT.md","SECURITY.md","SUPPORT.md","LICENSE"
        WRITE   ".github/ISSUE_TEMPLATE/*",".github/PULL_REQUEST_TEMPLATE.md"
        RET

WRITE_PROJECT_CHARTERS:
        WRITE   "docs/governance/CHARTER.md"
        WRITE   "docs/governance/SIG-ARCH-CHARTER.md"
        WRITE   "docs/governance/RFC-PROCESS.md"
        RET

SET_CODEOWNERS_AND_OWNERSHIP:
        WRITE   "CODEOWNERS"                 ; require W11 + SIG-Arch on NoC/HAL/MEMMAP
        WRITE   "MAINTAINERS.md"
        RET

ESTABLISH_DATA_POLICY:
        WRITE   "docs/build/LFS-POLICY.md"   ; *.gds, *.bit, waves → LFS
        WRITE   "docs/build/ARTIFACTS-POLICY.md" ; long-run artifacts → bucket/registry
        RET

PIN_NIX_FLAKES:
        CMD     "nix flake init && nix flake lock"
        RET

CONFIG_BAZEL_RULES:
        WRITE   ".bazelrc","WORKSPACE","BUILD" ; per-tree targets (//hw,//sw,//tools)
        RET

MAKE_SHIMS:
        WRITE   "mk"                          ; ./mk build, ./mk test, ./mk fmt
        RET

SPARSE_WORKFLOWS:
        WRITE   "docs/build/SPARSE-CHECKOUT.md"
        CMD     "git sparse-checkout set docs workstreams/W11_system-integration"
        RET

WRITE_ARCH_OVERVIEWS:
        WRITE   "docs/arch/OVERVIEW.md","docs/arch/SOC-TOP-LEVEL.md"
        WRITE   "docs/arch/INTERFACES/NOC-SPEC.md","docs/arch/INTERFACES/DRIVER-HAL.md","docs/arch/MEMORY-MAP.md"
        RET

RFC_TEMPLATES_ADR:
        WRITE   "docs/templates/RFC-TEMPLATE.md","docs/templates/ADR-TEMPLATE.md"
        RET

ONBOARDING_TRACKS:
        WRITE   "docs/onboarding/SOFTWARE.md","docs/onboarding/HARDWARE.md","docs/onboarding/CI-ADMINS.md"
        RET

CI_PATH_FILTERS:
        WRITE   ".github/workflows/ci.yml"    ; with paths-filter to run only affected targets
        WRITE   ".github/workflows/docs.yml"
        RET

CI_REMOTE_CACHE:
        CONFIG  "bazel-remote"                ; cache + (optional) RBE workers
        RET

CI_TIERS_SOFT:
        DEFINE  "tier0: lint/format", "tier1: unit", "tier2: integration"
        RET

DOCS_CHECKS:
        ADD     "markdownlint", "linkcheck", "adr-lint"
        RET

PROVISION_SELF_HOSTED:
        NOTES   "GH Actions self-hosted runners: 32c/128GB/NVMe; autoscale"
        RET

HW_CI_TIERS:
        DEFINE  "PR: verible + very-fast sims (<15m)",
                "main: regression sims (hours)",
                "nightly: synth/PnR/STA per platform"
        RET

EDA_ENVS_NIX:
        PIN     "yosys","openroad","verilator" via Nix
        RET

HW_ARTIFACT_RETENTION:
        ROUTE   "waves, checkpoints → artifacts store (not git)"
        RET

SBOM_SOFTWARE:
        PIPE    "generate SBOM on release"
        RET

HBOM_HARDWARE:
        DEFINE  "docs/templates/HW-IP-METADATA.yaml"
        AGGREG  "per-IP metadata → HBOM at release"
        RET

REQUIRE_TSC_SIGARCH:
        ENFORCE "CODEOWNERS on NoC/HAL/MEMMAP → W11 + SIG-Arch + W6"
        RET

RFC_STAGES_ENFORCED:
        STATES  "proposal → implementable → implemented"; block merges pre-approval
        RET

MAINTAINERS_ROLES:
        UPDATE  "docs/governance/MAINTAINERS-GUIDE.md"
        RET

WS_SCAFFOLD(W):
        MKDIR   "workstreams/W{W}/"
        WRITE   "workstreams/W{W}/README.md","ARCHITECTURE.md","INTERFACE.md","VERIFICATION_PLAN.md","ROADMAP.md"
        RET

WS_MIN_TESTS(W):
        ADD     "smoke tests to //workstreams/W{W}/verification:smoke_test"
        WIRE    "to PR pipeline"
        RET

OPEN_ISSUE_OR_RFC:
        TEMPLATE "bug/feature/RFC templates"
        RET

CREATE_BRANCH:
        NAME    "feature/w{W}-area/short-desc"
        RET

CODE_AND_TEST:
        CMD     "nix develop -- bazel test //... (scoped)"
        RET

RUN_PRECOMMIT:
        CMD     "./mk fmt && ./mk lint && ./mk hdl-lint"
        RET

PUSH_PR:
        CMD     "git push && open PR with template"
        RET

AWAIT_REQUIRED_REVIEWS:
        BLOCK   "until CODEOWNERS approvals obtained"
        RET

CI_RESULT_CHECK:
        IF      "CI == red" THEN RET_TO FIX_AND_RETRY ELSE RET

SQUASH_OR_MERGE:
        POLICY  "DCO enforced; squash preferred"
        RET

MAIN_BRANCH_JOBS:
        RUN     "extended regressions; coverage publish"
        RET

NIGHTLY_JOBS:
        RUN     "synth/PnR/STA; long sims; docs site"
        RET

VERSION_BUMP:
        APPLY   "SemVer; HW variants doc'd in releases/VERSIONING.md"
        RET

AGGREGATE_SBOM_HBOM:
        COMBINE "software SBOM + hardware HBOM → release assets"
        RET

TAG_AND_SIGN:
        CMD     "git tag -s vX.Y.Z && cosign attest"
        RET

GITHUB_RELEASE:
        CREATE  "release with notes + artifacts"
        RET

DISCUSSIONS_TRIAGE:
        CADENCE "weekly triage, label hygiene"
        RET

GOOD_FIRST_ISSUES:
        CURATE  "onboarding-friendly backlog"
        RET

METRICS_AND_HEALTH:
        DASH    "CI time, flake rate, coverage, release cadence"
        RET

RETROSPECTIVES:
        MEET    "monthly retro: infra pain, policy updates"
        RET

ROADMAP_UPDATE:
        EDIT    "docs/roadmap/MILESTONES.md (v0.1→v0.2→v0.4)"
        RET

DOCS_DRIFT_CHECK:
        SCAN    "specs vs code drift; open RFCs to correct"
        RET
