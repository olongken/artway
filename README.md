// navigation.tsx
import { Link, useLocation } from "wouter";
import { cn } from "@/lib/utils";
import { Book, Image, Video, GraduationCap } from "lucide-react";
import { SearchBar } from "./search-bar";

export function Navigation() {
  const [location] = useLocation();

  const navItems = [
    { href: "/notes", label: "Nota", icon: Book },
    { href: "/infographics", label: "Infografik", icon: Image },
    { href: "/videos", label: "Video", icon: Video },
    { href: "/exam-guide", label: "Panduan Peperiksaan", icon: GraduationCap },
  ];

  return (
    <header className="bg-white border-b sticky top-0 z-50">
      <div className="container mx-auto">
        <div className="flex flex-col py-4">
          <div className="flex items-center justify-between mb-4">
            <Link href="/">
              <a className="text-2xl font-bold text-primary hover:text-primary/90">
                Seni Visual T6
              </a>
            </Link>
            <SearchBar className="w-96" />
          </div>

          <nav className="flex justify-start space-x-1">
            {navItems.map(({ href, label, icon: Icon }) => (
              <Link key={href} href={href}>
                <a>
                  <button
                    className={cn(
                      "flex items-center gap-2 px-4 py-2 rounded-md transition-colors",
                      location === href
                        ? "bg-primary text-white"
                        : "text-gray-600 hover:bg-gray-100"
                    )}
                  >
                    <Icon className="h-4 w-4" />
                    <span>{label}</span>
                  </button>
                </a>
              </Link>
            ))}
          </nav>
        </div>
      </div>
    </header>
  );
}

// content-card.tsx
import { Card, CardContent } from "@/components/ui/card";
import { type Content } from "@shared/schema";
import { Badge } from "@/components/ui/badge";

interface ContentCardProps {
  content: Content;
}

export function ContentCard({ content }: ContentCardProps) {
  return (
    <Card className="overflow-hidden hover:shadow-lg transition-shadow">
      {content.imageUrl && (
        <div className="relative aspect-[4/3] w-full overflow-hidden">
          <img
            src={content.imageUrl}
            alt={content.title}
            className="w-full h-full object-cover transition-transform hover:scale-105"
          />
        </div>
      )}
      <CardContent className="p-4">
        <h3 className="font-semibold text-lg mb-2">{content.title}</h3>
        <p className="text-sm text-muted-foreground mb-3 line-clamp-2">
          {content.description}
        </p>
        <div className="flex gap-2 flex-wrap">
          {content.tags?.map((tag) => (
            <Badge key={tag} variant="outline" className="text-xs">
              {tag}
            </Badge>
          ))}
        </div>
      </CardContent>
    </Card>
  );
}

// schema.ts
import { pgTable, text, serial } from "drizzle-orm/pg-core";
import { createInsertSchema } from "drizzle-zod";
import { z } from "zod";

export const contentTypes = ["note", "infographic", "video", "exam-guide"] as const;
export type ContentType = typeof contentTypes[number];

export const contents = pgTable("contents", {
  id: serial("id").primaryKey(),
  title: text("title").notNull(),
  description: text("description").notNull(),
  type: text("type", { enum: contentTypes }).notNull(),
  content: text("content").notNull(),
  imageUrl: text("image_url"),
  videoUrl: text("video_url"),
  category: text("category").notNull(),
  tags: text("tags").array(),
});

export const insertContentSchema = createInsertSchema(contents).omit({ id: true });
export type InsertContent = z.infer<typeof insertContentSchema>;
export type Content = typeof contents.$inferSelect;

// theme.json
{
  "variant": "professional",
  "primary": "hsl(220 70% 50%)",
  "appearance": "light",
  "radius": 0.5
}
