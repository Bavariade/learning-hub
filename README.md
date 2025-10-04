"""
repository.py

A robust, single-file Repository pattern implementation suitable for a GitHub repo's
"repository" section. This example is written in Python and demonstrates:
- dataclass-based entities with versioning
- thread-safe in-memory store with optional JSON persistence
- full CRUD (create, read, update, delete) with soft delete support
- search, filter, sort, and pagination helpers
- bulk operations, audit logging, and simple event hooks
- small CLI-style usage example & basic self-test at the bottom

Drop this file into a project, adapt the Entity fields and persistence path as needed.
"""

import json
import uuid
import threading
import datetime
from dataclasses import dataclass, field, asdict
from typing import Any, Callable, Dict, Generic, Iterable, List, Optional, Sequence, Tuple, TypeVar

T = TypeVar("T", bound="BaseEntity")


# ----------------------------------------
# Domain model
# ----------------------------------------
@dataclass
class BaseEntity:
    id: str = field(default_factory=lambda: str(uuid.uuid4()))
    created_at: str = field(default_factory=lambda: datetime.datetime.utcnow().isoformat() + "Z")
    updated_at: str = field(default_factory=lambda: datetime.datetime.utcnow().isoformat() + "Z")
    version: int = 1
    deleted: bool = False
    metadata: Dict[str, Any] = field(default_factory=dict)

    def touch(self):
        self.updated_at = datetime.datetime.utcnow().isoformat() + "Z"
        self.version += 1


@dataclass
class Item(BaseEntity):
    name: str = ""
    description: str = ""
    tags: List[str
