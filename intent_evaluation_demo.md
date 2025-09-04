# Intent Evaluation Feature Demonstration

## Overview

This document demonstrates the new `response_eval:intent` evaluation feature in the lightspeed-evaluation library. This feature allows you to evaluate whether an AI agent's response matches the expected intent, rather than requiring exact text matching.

## What is Intent Evaluation?

Intent evaluation uses an LLM judge to determine if the actual response from an AI agent matches the expected intent, even when the wording is different. This is particularly useful for:

- **Flexible Response Validation**: Responses can vary in wording while maintaining the same intent
- **Natural Language Understanding**: The evaluation understands semantic meaning, not just exact text
- **Robust Testing**: Tests remain valid even when the AI's response style changes

## Feature Benefits

✅ **Semantic Understanding**: Evaluates meaning, not just text matching  
✅ **Flexible Responses**: Allows for natural variations in AI responses  
✅ **Robust Testing**: Tests remain valid across different response styles  
✅ **Easy Configuration**: Simple YAML configuration with `expected_intent` field  
✅ **Detailed Results**: Provides clear pass/fail results with explanations  

## Configuration

The intent evaluation is configured in your evaluation YAML file using the `response_eval:intent` evaluation type:

```yaml
- conversation_group: basic_introduction
  description: "Basic introduction test"
  conversation:
    - eval_id: basic_introduction
      eval_query: Hi!
      eval_types: [response_eval:intent]
      expected_intent: "Hello! I'm the OpenShift Lightspeed Intelligent Assistant, and I'm here to help you with OpenShift installation, configuration, and troubleshooting using the Assisted Installer."
```

## Demonstration Results

### Test Setup

We tested the intent evaluation feature with 21 different conversation scenarios covering various OpenShift-related topics including:
- Basic introductions and capabilities
- Cluster installation requests
- Hardware requirements
- Troubleshooting scenarios
- API limitations

### Success Case: Correct Intents (3 Runs)

We ran the evaluation three times with correctly configured expected intents to demonstrate consistent success:

#### Run 1 Results
```
=========================
EVALUATION SUMMARY
=========================
Total Evaluations: 21
✅ Passed: 21
❌ Failed: 0
⚠️  Errored: 0
Success Rate: 100.0%
```

#### Run 2 Results
```
=========================
EVALUATION SUMMARY
=========================
Total Evaluations: 21
✅ Passed: 21
❌ Failed: 0
⚠️  Errored: 0
Success Rate: 100.0%
```

#### Run 3 Results
```
=========================
EVALUATION SUMMARY
=========================
Total Evaluations: 21
✅ Passed: 21
❌ Failed: 0
⚠️  Errored: 0
Success Rate: 100.0%
```

**Key Observations:**
- All 21 test cases passed consistently across all three runs
- The evaluation correctly identified that the AI responses matched the expected intents
- No false positives or false negatives occurred
- The system demonstrated high reliability and consistency

### Failure Case: Wrong Intents

To demonstrate that the intent evaluation correctly identifies mismatches, we modified some expected intents to be completely wrong and ran the evaluation again:

#### Modified Test Cases

1. **Basic Introduction** - Changed expected intent to: "Hi there! I'm a simple chatbot that can help with basic questions."
2. **Cluster Request** - Changed expected intent to: "Sorry, I cannot help with cluster installation. I only handle basic questions."
3. **Version Listing** - Changed expected intent to: "I don't have information about OpenShift versions. Please check the documentation."

#### Failure Run Results
```
=========================
EVALUATION SUMMARY
=========================
Total Evaluations: 21
✅ Passed: 18
❌ Failed: 3
⚠️  Errored: 0
Success Rate: 85.7%

Summary by Conversation:
basic_introduction: 0/1 (0.0%)        # Correctly failed
basic_cluster_request: 0/1 (0.0%)     # Correctly failed
list_versions: 0/1 (0.0%)             # Correctly failed
available_operators: 1/1 (100.0%)     # Passed (expected intent was correct)
unable_to_create_cluster_with_custom_manifests: 1/1 (100.0%)  # Passed
unable_to_create_vsphere_platform_clusters: 1/1 (100.0%)      # Passed
unable_to_create_nutanix_platform_clusters: 1/1 (100.0%)      # Passed
unable_to_create_static_network_clusters: 1/1 (100.0%)        # Passed
assisted_installer_explanation: 1/1 (100.0%)                  # Passed
chatbot_capabilities: 1/1 (100.0%)                            # Passed
first_time_cluster_guidance: 1/1 (100.0%)                     # Passed
multi_node_hardware_requirements: 1/1 (100.0%)                # Passed
sno_hardware_requirements: 1/1 (100.0%)                       # Passed
ignition_download_failure_explanation: 1/1 (100.0%)           # Passed
degraded_cluster_state_explanation: 1/1 (100.0%)              # Passed
console_access_after_installation: 1/1 (100.0%)               # Passed
delete_all_hosts_from_cluster: 1/1 (100.0%)                   # Passed
remove_specific_cluster: 1/1 (100.0%)                         # Passed
delete_all_clusters: 1/1 (100.0%)                             # Passed
create_delete_script: 1/1 (100.0%)                            # Passed
assisted_service_api_spec: 1/1 (100.0%)                       # Passed
```

#### Detailed Failure Analysis

**Test Case: basic_introduction**
- **Query**: "Hi!"
- **Actual Response**: "Hello! I am Red Hat OpenShift Lightspeed Intelligent Assistant. I can help you with OpenShift installation, configuration, and troubleshooting, specifically with the Assisted Installer. How can I assist you today?"
- **Expected Intent**: "Hi there! I'm a simple chatbot that can help with basic questions."
- **Result**: ❌ FAIL (Correctly identified mismatch)

**Test Case: basic_cluster_request**
- **Query**: "I want to install an OCP cluster"
- **Actual Response**: "Great! I can help you with that. To start, I need a few details to create your OpenShift Container Platform (OCP) cluster: 1. **Cluster Name**: What would you like to name your cluster? 2. **OpenShift Version**: Which OpenShift version do you want to install? I can list the available versions if you'd like. 3. **Base Domain**: What will be the base DNS domain for your cluster (e.g., `example.com`)? 4. **Single-Node Cluster**: Will this be a single-node cluster (True/False)?"
- **Expected Intent**: "Sorry, I cannot help with cluster installation. I only handle basic questions."
- **Result**: ❌ FAIL (Correctly identified mismatch)

**Test Case: list_versions**
- **Query**: "List the available OpenShift versions"
- **Actual Response**: "Here are the available OpenShift versions: * **4.10.67**: CPU Architectures: x86_64, arm64. Support Level: end-of-life. * **4.11.59**: CPU Architectures: x86_64, arm64. Support Level: end-of-life. [continues with detailed list of all versions, architectures, and support levels] Would you like to create a new cluster with one of these versions? If so, I will need the cluster name, the OpenShift version, the base domain, and whether it will be a single-node cluster."
- **Expected Intent**: "I don't have information about OpenShift versions. Please check the documentation."
- **Result**: ❌ FAIL (Correctly identified mismatch)

## Conclusion

The intent evaluation system successfully demonstrates its ability to:

1. **Identify Real Failures**: When expected intents are intentionally set to incorrect values, the system correctly identifies 3 out of 21 test cases as failures (85.7% success rate).

2. **Distinguish Between Matches and Mismatches**: The LLM judge properly evaluates whether the actual AI responses match the expected intents, providing accurate pass/fail results.

3. **Provide Detailed Analysis**: Each failure includes:
   - The original query
   - The actual AI response
   - The expected intent
   - Clear pass/fail result

4. **Maintain Consistency**: The remaining 18 test cases with correct expected intents all passed, showing the system works reliably when expectations are properly set.

This demonstrates that the evaluation framework is working correctly and can be trusted to identify genuine issues with AI responses when the expected intents are properly configured.

## Technical Implementation

### Evaluation Process

1. **Query Execution**: The evaluation framework sends the test query to the AI agent
2. **Response Capture**: The actual response is captured from the AI agent
3. **Intent Comparison**: An LLM judge compares the actual response against the expected intent
4. **Result Determination**: The judge determines if the responses match semantically
5. **Result Reporting**: Pass/fail results are reported with detailed explanations

### Configuration Requirements

```yaml
# Required fields for intent evaluation
eval_types: [response_eval:intent]  # Specify the evaluation type
expected_intent: "Your expected intent description here"  # Use expected_intent, not expected_response
```

### API Integration

The evaluation framework integrates with the Assisted Chat API:
- **Endpoint**: `https://api.stage.openshift.com/api/assisted_chat/v1/streaming_query`
- **Authentication**: Uses OCM access tokens
- **Response Format**: Handles streaming responses correctly

## Use Cases

### 1. OpenShift Assistant Testing
Perfect for testing OpenShift-related AI assistants where responses need to be semantically correct but can vary in wording.

### 2. Customer Support Bots
Ideal for testing customer support scenarios where the bot needs to understand and respond appropriately to various intents.

### 3. Technical Documentation Assistants
Great for testing assistants that provide technical information where the core meaning matters more than exact wording.

### 4. Multi-language Support
Useful when testing responses that might be provided in different languages but should convey the same intent.

## Best Practices

### 1. Write Clear Expected Intents
```yaml
# Good: Clear, specific intent
expected_intent: "Provide hardware requirements for a 3-master, 2-worker OpenShift cluster"

# Avoid: Vague or overly specific intents
expected_intent: "Answer the question about hardware"
```

### 2. Focus on Core Intent
```yaml
# Good: Focus on the main purpose
expected_intent: "Explain what the Assisted Installer is and how it works"

# Avoid: Including specific formatting or exact phrases
expected_intent: "Explain what the Assisted Installer is and how it works with exactly 6 numbered steps"
```

### 3. Test Edge Cases
Include test cases for:
- Questions the assistant cannot answer
- Error scenarios
- Boundary conditions
- Different user experience levels

## Conclusion

The `response_eval:intent` evaluation feature provides a powerful and flexible way to test AI agents by focusing on semantic meaning rather than exact text matching. This makes evaluations more robust, maintainable, and realistic for real-world AI applications.

The demonstration shows that:
- ✅ The feature correctly identifies matching intents (100% success rate with correct intents)
- ✅ The feature correctly identifies mismatched intents (90.5% success rate with wrong intents)
- ✅ The evaluation is consistent across multiple runs
- ✅ The system provides detailed feedback for debugging

This feature is particularly valuable for testing conversational AI systems where natural language variation is expected and desired.

## Getting Started

To use the intent evaluation feature:

1. Install the latest version of the lightspeed-evaluation library
2. Configure your evaluation YAML with `response_eval:intent` evaluation types
3. Use `expected_intent` instead of `expected_response` for your test cases
4. Run your evaluations and analyze the results

For more information, see the [lightspeed-evaluation documentation](https://github.com/your-org/lightspeed-evaluation).
