# 任务书 Schema

> 任务书是教研组的唯一权威信息源。9问问诊答案直接映射到字段。

## 字段定义

```json
{
  "task_id": "CD-YYYYMMDD-NNN",
  "created_at": "ISO8601",
  "created_by": "教务长",
  "version": "1.0",

  "user_profile": {
    "planner_name": "string,        // 你是谁
    "planner_role": "string,         // 用户角色
    "planner_experience": "string"   // 经验水平
  },

  "course_meta": {
    "topic": "string,                // 课程主题
    "target_audience": "string,      // 给谁讲（学员画像：角色/经验/人数）
    "business_goal": "string,        // 为啥要（业务痛点+期望业务结果）
    "duration_hours": "number,       // 课时
    "style": "string,                // 风格偏好
    "urgency": "high|medium|low"     // 紧急程度
  },

  "constraints": {
    "venue": "string,                // 场地约束
    "equipment": "string,            // 设备约束
    "industry_sensitivity": "string",// 行业敏感
    "special": "string"              // 其他特殊约束
  },

  "deliverables": {
    "instructor_manual": true,       // 讲师手册
    "learner_manual": true,          // 学员手册
    "slides": true,                  // 课件PPT
    "exercises": true,               // 习题集
    "lesson_plan": true,             // 备课地图
    "case_library": true,            // 案例集
    "flashcards": false,             // 教具/知识卡片
    "course_spec": true              // 课程说明书（教务长自动产出）
  },

  "stages": {
    "stage1_topic":      {"status": "pending|in_progress|approved|rejected", "assignee": "课程规划师", "started_at": null, "completed_at": null},
    "stage2_goal":       {"status": "pending", "assignee": "课程规划师", "started_at": null, "completed_at": null},
    "stage3_structure":  {"status": "pending", "assignee": "结构设计师", "started_at": null, "completed_at": null},
    "stage4_content":    {"status": "pending", "assignee": "内容开发者", "started_at": null, "completed_at": null},
    "stage5_activity":   {"status": "pending", "assignee": "教学活动师", "started_at": null, "completed_at": null},
    "stage6_package":    {"status": "pending", "assignee": "课件制作师+案例编撰师+习题编写师", "started_at": null, "completed_at": null},
    "stage7_review":     {"status": "pending", "assignee": "教务长+全员", "started_at": null, "completed_at": null},
    "stage8_project":    {"status": "skipped", "assignee": "培训项目师", "started_at": null, "completed_at": null}
  },

  "rejections": {
    "stage1": {"manager_rejections": 0, "user_rejections": 0},
    "stage2": {"manager_rejections": 0, "user_rejections": 0},
    "stage3": {"manager_rejections": 0, "user_rejections": 0},
    "stage4": {"manager_rejections": 0, "user_rejections": 0},
    "stage5": {"manager_rejections": 0, "user_rejections": 0},
    "stage6": {"manager_rejections": 0, "user_rejections": 0},
    "stage7": {"manager_rejections": 0, "user_rejections": 0}
  },

  "changelog": [
    {
      "version": "1.0",
      "timestamp": "ISO8601",
      "author": "教务长",
      "reason": "初始创建",
      "changes": "任务书创建"
    }
  ]
}
```

## 9问 → 字段映射

| 问诊项 | 对应字段 |
|---|---|
| 1 你是谁 | `user_profile.planner_name` + `planner_role` + `planner_experience` |
| 2 给谁讲 | `course_meta.target_audience` |
| 3 为啥要 | `course_meta.business_goal`（业务痛点部分）|
| 4 你要啥 | `deliverables.*`（逐项确认）|
| 5 痛点 | `course_meta.business_goal`（学员问题部分）|
| 6 期望 | `course_meta.business_goal`（业务结果部分）|
| 7 时长 | `course_meta.duration_hours` |
| 8 风格 | `course_meta.style` |
| 9 输出 | `course_meta.urgency` + `constraints.*` |

## 任务书修订流程

1. 组员提议修订 → 教务长评估
2. 教务长决定修订 → 更新字段 + version+1
3. changelog 追加：`{version, timestamp, author:"教务长", reason, changes}`
4. 通知所有相关组员（通过教务长转达）
5. 若涉及交付物配置 → 提交用户确认

## 验收状态机

```
pending → in_progress → approved (进入下一阶段)
                     └→ rejected (打回重做)
                          └→ in_progress (重做循环)
                              └→ 超过3次打回 → 升级
```

 стадии8 状态机特殊：`skipped` → `in_progress` → `approved`（用户明确需要才从skipped转出）